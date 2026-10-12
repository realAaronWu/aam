## Context

Status: proposed architecture, not an implemented system. See [proposal](proposal.md), [stories](../../../docs/product/user-stories.md) and [decision register](../../../docs/product/decisions.md). Business-owner questions are activation prerequisites, not engineering-authored answers.

## Goals / Non-Goals

**Goals:** durable case coordination; independently tunable mail understanding; scoped, evidence-backed actions; extensible client workflows; maintainable managed Azure deployment. Deliver a deterministic quarterly local reporting slice before coupling the journeys to model-generated replies.

**Non-Goals:** autonomous underwriting; an LLM controlling authorization; one service per specialist; self-hosted databases/brokers/workflow servers; deciding unresolved business policy. This change implements no application and provisions no services.

## Decisions

### Components and data flow

There are four deployables: portal/API, intake/synchronization, workflow/agent workers, and delivery workers. The diagram's modules are logical components inside these deployables, not independently deployed microservices. All authoritative writes use a shared domain/policy library. AI Search is added for the mail-intelligence slice; Service Bus is deferred.

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontFamily": "Arial, sans-serif", "fontSize": "15px", "primaryTextColor": "#172033", "lineColor": "#64748b", "edgeLabelBackground": "#ffffff"}, "flowchart": {"htmlLabels": false, "curve": "linear", "nodeSpacing": 35, "rankSpacing": 55}}}%%
flowchart TB
  AM[Account Manager and administrator] <--> API[Portal and API]
  MAIL[Microsoft Graph] -->|Folder delta polling: inbound and Sent Items| IN[Intake and synchronization]
  CRM[Sales CRM] -->|Read approved facts| IN
  IN -->|Originals first: hash and scoped reference| BLOB[(Blob: originals, packages, evidence)]
  IN -->|One transaction: message, cursor, inbox and outbox| DB[(PostgreSQL: authoritative cases, obligations, timers, commands)]
  API <-->|Shared domain and policy library| DB
  API -->|Reviewed immutable package objects| BLOB
  subgraph WORKERS[Workflow and agent worker deployable]
    RELAY[Outbox relay]
    SWEEP[Due-timer and renewal scan]
    CORE[State transition handler and policy library]
    CTX[Trusted context builder]
    AI[Bounded specialist stages]
    IDX[Knowledge indexer]
  end
  DB -->|Claim pending work| RELAY
  RELAY -->|Start deterministic work instance| DTS[Managed Durable Task Scheduler]
  DTS <-->|Short-lived activities| CORE
  SWEEP <-->|Due rows and cached CRM eligibility; enqueue atomically| DB
  CORE <-->|Inbox, versions, decisions and outgoing work in one transaction| DB
  CORE --> CTX
  CTX -->|Scoped facts, history and versions| DB
  CTX -->|Permitted source objects| BLOB
  CTX --> AI
  AI -->|Scoped research| SEARCH[Azure AI Search: derived index]
  AI --> MODEL[Foundry model deployments]
  AI -->|Typed proposals only| CORE
  DB -->|Approved knowledge revisions and index jobs| IDX
  BLOB -->|Approved content| IDX
  IDX --> SEARCH
  DB -->|Persisted command claim| OUT[Delivery worker with shared policy library]
  OUT <-->|Recheck, attempts, provider IDs and outcomes| DB
  OUT -->|Create correlated draft then send| MAIL
  OUT -->|Optional approved write-back| CRM
  subgraph PLATFORM[Managed platform services]
    ENTRA[Entra and workload identity]
    KV[Key Vault]
    MON[Azure Monitor and App Insights]
    ACR[Azure Container Registry]
  end
  PLATFORM -.-> API
  PLATFORM -.-> IN
  PLATFORM -.-> WORKERS
  PLATFORM -.-> OUT

  %% Core nodes have stronger borders; infrastructure remains visually secondary.
  classDef default fill:#f8fafc,stroke:#94a3b8,color:#172033,stroke-width:1.5px
  classDef intake fill:#eff6ff,stroke:#2563eb,color:#1e3a8a,stroke-width:2px
  classDef authority fill:#dbeafe,stroke:#1e40af,color:#172554,stroke-width:4px,font-weight:bold
  classDef core fill:#ccfbf1,stroke:#0f766e,color:#134e4a,stroke-width:3px,font-weight:bold
  classDef intelligence fill:#f5f3ff,stroke:#7c3aed,color:#4c1d95,stroke-width:2px
  classDef evidence fill:#fffbeb,stroke:#b45309,color:#78350f,stroke-width:2px
  classDef delivery fill:#ffedd5,stroke:#c2410c,color:#7c2d12,stroke-width:4px,font-weight:bold
  classDef platform fill:#f1f5f9,stroke:#94a3b8,color:#475569,stroke-width:1px
  class IN,MAIL,CRM intake
  class DB authority
  class RELAY,DTS,CORE core
  class CTX,AI,SEARCH,MODEL intelligence
  class BLOB,IDX evidence
  class OUT delivery
  class ENTRA,KV,MON,ACR platform
  style WORKERS fill:#f0fdfa,stroke:#0f766e,stroke-width:2px,color:#134e4a
  style PLATFORM fill:#f8fafc,stroke:#cbd5e1,stroke-width:1px,color:#475569

  %% Link indexes follow the connection statements above; keep in sync when editing.
  %% Sky: human/API interaction. Blue: intake and original persistence.
  linkStyle 0,5 stroke:#0369a1,stroke-width:2px,color:#075985
  linkStyle 1,2,3 stroke:#2563eb,stroke-width:2.5px,color:#1e40af
  linkStyle 4 stroke:#2563eb,stroke-width:4px,color:#1e40af
  %% Teal: critical durable execution and authoritative state transitions.
  linkStyle 7,8,9,10,11 stroke:#0f766e,stroke-width:4px,color:#115e59
  %% Violet: context, model calls, research and typed proposals.
  linkStyle 12,13,14,15,16,17,18 stroke:#7c3aed,stroke-width:2.5px,color:#5b21b6
  %% Amber: package/evidence storage and derived indexing.
  linkStyle 6,19,20,21 stroke:#b45309,stroke-width:2px,color:#92400e
  %% Orange: critical command claim, final recheck and external send.
  linkStyle 22,23,24 stroke:#c2410c,stroke-width:4px,color:#9a3412
  linkStyle 25 stroke:#c2410c,stroke-width:2px,stroke-dasharray:6 4,color:#9a3412
  %% Slate: supporting platform dependencies, not business events.
  linkStyle 26,27,28,29 stroke:#94a3b8,stroke-width:1.5px,stroke-dasharray:4 4,color:#475569
```

**Connection colors:** blue = intake; teal = durable execution and state changes; orange = external delivery; violet = agent/context processing; amber = evidence and indexing; sky blue = human/API interaction; dashed gray = platform support. Dashed orange is optional CRM write-back.

**Critical path (thick lines):** intake → PostgreSQL → outbox relay → Durable Task → state handler → PostgreSQL → delivery worker → Microsoft Graph. PostgreSQL, the execution core and delivery worker have bold borders. Follow the labels and arrowheads as well as color; a bidirectional edge represents separate reads/writes, not an atomic cross-service operation.

**ADR-01 Specialist implementation — revised:** Python/Pydantic adapters and direct structured model calls for analyzer, writer and semantic validator. Research alone may use a bounded tool loop. Use typed schemas, an evidence registry and content-addressed bundles; do not import Deep Agents/LangChain/LangGraph as mandatory runtime dependencies. They are alternatives if measured needs justify planning or graph features later. A .NET rewrite is unnecessary for Azure hosting. A free-form supervisor is excluded because it cannot establish trusted context or authorization.

**ADR-02 Execution ownership — superseded in part by ADR-06:** Durable Task runs bounded technical work with activity retry/checkpoints. No orchestration waits for a business reply or approval for months. Model calls and external I/O occur in activities, not deterministic orchestration code. Technical checkpoints never become authoritative case state. Process-local scheduling alone is insufficient for restart/replica safety.

**ADR-03 Persistence and event path — revised:** one managed PostgreSQL business store, Blob for originals/artifacts, DTS for short-lived execution, AI Search as a derived index for the intelligence slice. Outbox relay → DTS → transition activities is the sole general event-processing path. Delivery workers claim persisted command rows directly; a command-ready outbox event is only a latency hint, not a second dispatch authority. Service Bus is deferred until external consumers or independently scaled fan-out justify it. PostgreSQL full-text/pgvector is an alternative to AI Search, not an assumed improvement: compare retrieval quality, indexing operations and DB load before substituting it. The deterministic slice needs neither search nor a broker.

**ADR-04 Authorization — strengthened:** specialists propose; shared deterministic code authorizes. Approval dependencies include the exact rendered message, request disposition and referenced business versions. Unrelated case changes do not invalidate approval. Unknown policy disables the affected action. Human acknowledgment of factual warnings requires approved override authority and never bypasses account/recipient scope, stale-state checks or prohibited actions. There is no blanket “human approved, therefore safe” path.

**ADR-05 Extension model — revised:** code-reviewed workflow types define allowed primitives and schema; business-administered client parameters fill bounded slots. Cases pin both. New steps/tools/permissions require engineering review. The parameter API may change only fields authorized by the type and business policy; no arbitrary scripts. Unknown evidence/promotion/recipient rules remain unresolved slots and block activation, not guessed defaults.

**ADR-06 Single long-lived state owner — accepted:** PostgreSQL owns cases, obligations, waits, approvals and due timers. A timer row contains timer ID, obligation ID/version, due UTC instant, business calendar and parameter versions, status, lease and attempt metadata. The due sweep atomically claims due rows and creates a versioned work item/outbox record. The handler rechecks current DB state before any transition. Approval and reply arrivals create new work; they do not resume a months-old orchestration.

DTS instances are bounded work attempts: logical keys `msg:<mailbox>:<immutable-id>:<processing-revision>`, `evt:<event-id>` and `tmr:<timer-id>:<version>`. The SDK-facing ID uses a stable hash of this key plus recovery epoch and attempt number to fit provider limits. Attempt changes never change the business event/command ID. A work ledger records pending/started/completed/failed, DTS instance and attempt, deadline, lease and committed result. A watchdog compares expired attempts with DTS status and recorded DB results before retrying; it quarantines poison work. Lost start acknowledgments are resolved by querying the same deterministic instance, not inventing a new business event. DB inbox keys and command uniqueness still protect duplicates after scheduler history expires.

Alternatives: long-lived DTS per case is viable with explicit replay/version routing and coordinated restore, but adds unnecessary cross-store recovery/version retention for these human-paced workflows. Pure DB workers would remove DTS but require us to implement technical activity retry/checkpoint infrastructure. Selected approach keeps managed execution and a single business authority; it does not make external side effects disposable. Old code compatibility remains necessary for active workflow types and short in-flight activities, with explicit migration before removal.

**ADR-07 Intake transport — accepted:** folder delta polling is the primary and only initial Graph intake path. Keep a cursor and health watermark per approved mailbox/folder, including Inbox, Sent Items and additional folders required by mailbox rules. Discover/approve folder changes; a move out of a polled folder is not proof a business message was deleted. Persist a page's durable records and cursor together. Invalid cursors trigger a controlled rescan with the same mailbox-scoped immutable-ID deduplication. Notifications are a future latency accelerator requiring a separately designed public ingress/Event Hubs path; there is no initial webhook or public machine ingress requirement. Portal access uses the organization's approved ingress independently. [Graph delta queries are per folder](https://learn.microsoft.com/en-us/graph/delta-query-messages).

**ADR-08 Provider dispatch — accepted with limits:** create a Graph draft with immutable-ID preference and an application command stamp; persist the ID before send. The delivery identity therefore needs scoped draft-write as well as send access. The chosen stamp is an app-specific single-value extended property whose create, filter and sent-copy preservation are qualified in T15.2; it is correlation, never proof of authority. Keep a provider-adapter boundary. Outlook-native draft review is an alternative, but is deferred: it changes the requested review/audit flow and requires business agreement and tenant validation. Initial review happens in AAM; observe human Outlook sends through intake.

**ADR-09 Isolation and operational backpressure — accepted:** independent application and Exchange recipient guards for non-production; effective mailbox scoping for each workload; bounded concurrency per dependency, mailbox and tenant; configured queue admission and retry budgets. Honors provider Retry-After and jittered exponential retry where safe. Backpressure retains durable work and exposes age, not silent drops. Model quotas do not bypass validation; send ambiguity does not become retryable merely because a budget resets.

### Transaction and trigger ownership

| Work | Owner, trigger and persistence | Failure/retry behavior |
| --- | --- | --- |
| Mail intake | Intake polls each approved folder, writes permitted original Blob object by hash, then message/inbox/outbox/cursor in one DB transaction | Blob failure prevents commit; DB failure leaves a collectible orphan, not acknowledged work. Cursor never skips a failed page. Orphan GC observes a grace period and committed reference check |
| CRM facts | Intake connector polls approved CRM read mappings and stores source version/time plus outbox | Retain explicit stale facts on outage; dependent actions hold; write-back is optional and separate |
| Renewal eligibility | Workflow worker scan uses due scan rows and authoritative cached CRM facts | Unique account/program/cycle key and outbox prevent duplicates; stale mandatory facts hold |
| Timers | Workflow due sweep uses DB leases and versioned rows | Recheck cancellation/version at handler and dispatch; overdue work recovered after downtime |
| Event relay | Workflow workers claim outbox and start recorded DTS instances | Mark completion only from durable handler result; duplicate start/event is harmless; watchdog resolves abandoned attempts |
| Knowledge indexing | Workflow indexer consumes approved knowledge revision jobs; reads Blob, applies account/effective-date metadata, updates derived Search index | Versioned upsert/delete, bounded retry and quarantine; stale/removed knowledge excluded by final DB authorization/version check |
| Package registry | API validates type/parameter versions, writes immutable Blob artifact then DB registry/hash and audit transaction | Unreferenced object GC as above; activation requires retrievable artifacts and approved business inputs |
| Delivery | Separate worker leases authorized command rows, rechecks using shared policy, persists attempt and draft ID, then calls provider | Fenced attempts, uncertainty state, correlated reconciliation; no generic retry of a possibly accepted send |

### Trusted context, validation and review

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontFamily": "Arial, sans-serif", "fontSize": "15px", "primaryTextColor": "#172033", "lineColor": "#64748b", "edgeLabelBackground": "#ffffff"}, "flowchart": {"htmlLabels": false, "curve": "linear", "nodeSpacing": 35, "rankSpacing": 55}}}%%
flowchart TD
  A[Observed inbound or outbound mail] --> B[Resolve scope and request links]
  B -->|Ambiguous or policy absent| H[Explicit review or configuration hold]
  B -->|Resolved current request| C[Freeze server-built context]
  C --> N[Analyze and propose obligations]
  N --> R[Scoped evidence research]
  R --> W[Write final text and suggested mappings]
  W --> V[Independent claim extraction and deterministic critical-span checks]
  V --> P{Shared policy decision}
  P -->|Automatic category and all gates pass| CMD[Persist authorized command]
  P -->|Requires review or factual warnings| UI[Review exact text and validation report]
  UI -->|Edit| V
  UI -->|Approve current text with permitted acknowledgments| CHECK[Recheck dependencies and non-overridable controls]
  CHECK --> CMD
  CHECK -->|Policy absent or failed control| H
  CMD --> SEND[Delivery claim, freshness barrier, draft then send]
  SEND --> END[Communication outcome; evaluate obligations separately]

  classDef default fill:#f8fafc,stroke:#94a3b8,color:#172033,stroke-width:1.5px
  classDef context fill:#eff6ff,stroke:#2563eb,color:#1e3a8a,stroke-width:2px
  classDef intelligence fill:#f5f3ff,stroke:#7c3aed,color:#4c1d95,stroke-width:2px
  classDef validation fill:#fef3c7,stroke:#b45309,color:#78350f,stroke-width:3px,font-weight:bold
  classDef policy fill:#ccfbf1,stroke:#0f766e,color:#134e4a,stroke-width:3px,font-weight:bold
  classDef delivery fill:#ffedd5,stroke:#c2410c,color:#7c2d12,stroke-width:4px,font-weight:bold
  classDef review fill:#e0f2fe,stroke:#0369a1,color:#0c4a6e,stroke-width:2px
  classDef hold fill:#fff1f2,stroke:#be123c,color:#881337,stroke-width:2px
  class A,B,C context
  class N,R,W intelligence
  class V validation
  class P,CHECK policy
  class CMD,SEND delivery
  class UI review
  class H hold

  %% Blue context, violet intelligence, amber independent validation.
  linkStyle 0,2 stroke:#2563eb,stroke-width:2.5px,color:#1e40af
  linkStyle 3,4,5 stroke:#7c3aed,stroke-width:2.5px,color:#5b21b6
  linkStyle 6,7 stroke:#b45309,stroke-width:4px,color:#92400e
  %% Teal authorization; sky human review; violet dashed edit/revalidation loop.
  linkStyle 8,11,12 stroke:#0f766e,stroke-width:4px,color:#115e59
  linkStyle 9 stroke:#0369a1,stroke-width:2.5px,color:#075985
  linkStyle 10 stroke:#7c3aed,stroke-width:2px,stroke-dasharray:6 4,color:#5b21b6
  %% Rose dashed holds; orange committed-command delivery/outcome path.
  linkStyle 1,13 stroke:#be123c,stroke-width:2.5px,stroke-dasharray:6 4,color:#9f1239
  linkStyle 14,15 stroke:#c2410c,stroke-width:4px,color:#9a3412
```

**Critical controls (bold borders and thick lines):** independent validation → policy/authorized approval → persisted command → final dispatch checks → outcome. Teal shows authorization, orange shows delivery, and amber marks the validation boundary. Sky blue is human review; dashed violet returns an edit for validation; dashed rose leads to an explicit hold. An automatic path still requires every configured gate to pass.

Resolver uses authorized CRM identifiers and workstream evidence. Broker/local team sender alone is insufficient. Legitimate multi-client email remains an explicit policy question; do not choose combined versus split replies for the owner. Hold unresolved multi-client processing without merging restricted contexts.

Context builder reads account-scoped DB history/facts and permitted Blob references, including latest observed human sends and synchronization health. A frozen snapshot records current message/body hash, source IDs, ordered case sequence, scope, data-use restrictions, truncation flags, workflow/parameter versions and bundle digest. Snapshot unavailability is failure; model output cannot substitute the current email or widen filters.

The validator reads final subject/body and rendered attachments where applicable, independently extracts claims, and unions those spans with the writer map and deterministic scans for numbers, dates, amounts, units and commitments. Unknown coverage or unsupported consequential text blocks automation. Scans are conservative flags, not proof every claim was found; adversarial and live evaluation must test omission and paraphrase. Semantic models cannot authorize sending. A human edit creates a new report/hash. The UI can accept policy-authorized factual-warning acknowledgments without repeatedly resubmitting unchanged text, while hard controls remain blocking. D-13 decides whether such overrides are allowed and by whom.

Proposed obligations are stored independently. Promotion requires an approved rule and authorized decision. No extracted due date creates an active timer by itself. A deterministic template path can bypass the LLM stages but must populate and validate the same command, scope, policy and dependency contracts.

### Reviewed examples: ingestion and runtime selection

**ADR-10 Keep examples inside the agent bundle:** use a small, Git-reviewed example library for US-02/US-07, covering renewal, quarterly local reporting and program-change correspondence. This is retrieved few-shot prompting, not model training. It adds files, a typed loader and a deterministic selection function to the existing workflow/agent worker. No additional deployable, database table, vector index, example-management UI or autonomous learning agent is required. The deterministic M2 quarterly reminder path is unchanged; examples join the M3 intelligence slice.

#### Minimal implementation

Use a typed resource loader for YAML agent/example definitions and Markdown prompts. Validate schema, labels, tool registration and resource paths at bundle load. Hash the actual prompt, schema and example bytes with selector settings, and retain the immutable contents for retry and rollback.

Analyzer and writer use separate structured inputs. Knowledge retrieval returns current authorized evidence; the in-process example selector supplies illustrative demonstrations. Direct structured model calls are sufficient; no agent framework or searchable example service is required.

#### Ingestion: a reviewed content change

1. **Select:** an AM/domain expert chooses a useful corrected reply or difficult intent decision from an authorized case, or authors a synthetic example. Start with representative contrasts: promise versus completed deck delivery; revised versus approved renewal terms; confirmed versus unverified program-change notice. Do not bulk-import mailbox history.
2. **Prepare:** remove client identities, personal data, secrets, actual prices and contractual details before committing. Preserve only synthetic context needed to understand the decision. A real source needs the existing data-use approval; a restricted source reference can stay in the existing protected case/audit record, outside the distributable bundle.
3. **Review:** the domain expert checks the expected intent, response/disposition and short rationale. Engineering adds the record to the relevant workflow YAML file and reviews schema, size, allowed labels and separation from evaluation data. Approval is recorded in the normal Git review/release process; an `approved: true` field alone is not approval. Draft candidates remain outside the released bundle. An AM approving a live email does not automatically publish it as an example.
4. **Check and publish:** CI validates the files and runs the targeted regression subset. Compare candidate and current bundles on held-out emails before claiming improvement. Publish through the existing prompt/schema release process, with retrievable immutable bundle contents retained in Blob and the worker release referencing their digest. No separate ingestion queue or hot-edit API is introduced.
5. **Improve:** periodically review existing AM corrections, abstentions and evaluation failures; manually curate another small content change. A production correction used for tuning cannot remain an unseen holdout. Roll back by selecting the previous compatible bundle for new runs; existing runs retain their pinned bundle. A defective bundle is suspended and affected unsent proposals reviewed, never silently changed during a retry.

Proposed files are `resources/examples/renewal.yaml`, `resources/examples/quarterly-delivery.yaml` and `resources/examples/program-changes.yaml`. Each has a schema version and an ordered list of records. These paths describe future deliverables, not files created by this planning change.

| Record fields | Purpose |
| --- | --- |
| `id`, `workflow_type`, `intent`, `language` | Stable identity and simple selection keys; workflow is an existing registered type |
| `incoming_email`, `context` | Synthetic/de-identified subject/body and the minimum illustrative prior turn or workflow state |
| `expected_analysis` | Correct intent, relevant entities/questions and ambiguity; includes hold/review examples |
| `expected_response` | Approved illustrative subject/body, or null when no response is appropriate |
| `expected_disposition`, `rationale` | Why to draft, clarify or hold; why a promise is not completion, for example |

`intent: any` is a reserved selection tag for a workflow-generic demonstration, never a predicted business intent. The bundle digest versions the entire record content; no additional per-record publishing service is needed. Initially all released examples are reusable, generic scenarios with no customer-specific confidential content. Client-specific policy and facts belong in authorized context/SOP retrieval. Future private client example collections require a separate scope design before introduction.

#### Runtime retrieval: local selection at two stages

1. **Load once:** each worker loads and validates the immutable bundle into memory. Unknown schemas/intents, duplicate IDs, missing required files or a digest mismatch make that bundle unavailable; they do not silently fall back to mutable files. An intentionally empty valid library is supported for baseline evaluation.
2. **Before analysis:** select classification demonstrations from the workflow already established by trusted case context. If no workflow is known, use a small balanced set across the three supported workflows. Do not require the yet-to-be-produced intent to select classifier examples. Pass only the illustrative email/context, expected analysis and classification rationale; no example reply is needed for classification. Ambiguous or multi-workflow mail remains explicit for coordinator review; examples never resolve account identity or authorize a case link.
3. **Before drafting:** after validated analysis and current evidence retrieval, select writing demonstrations by established workflow, proposed intent and language. Prefer exact intent/language matches, then explicitly marked workflow-generic examples in that language, with stable file order as the tie-break. For multiple questions, allocate slots across their intents rather than declaring coverage from one matching example. No match means zero examples; proceed under the same factual and review gates, never use an unrelated client's reply as a fallback.
4. **Bound and record:** initial tunable limits are at most six analysis demonstrations (up to two per workflow when unknown) and three writing demonstrations, plus a configured token budget per stage. Drop whole lowest-priority records when they do not fit; never truncate away an example's expected outcome or displace mandatory current context/evidence. Record bundle digest, selector version/settings, selected IDs and count/omission reasons in the existing run/stage metadata. These are engineering tuning defaults, not accuracy or automation thresholds.
5. **Separate inputs and authority:** the writer receives distinctly labeled current email/history, current verified facts/SOP evidence, and illustrative demonstrations. Examples teach phrasing and decisions; they do not enter the evidence registry, satisfy missing evidence, establish completed work, set deadlines or grant send authority. Current facts override illustrative dates/amounts. The independent validator receives actual text and current evidence, not example replies as reference truth. Existing authorization, approval and delivery checks apply unchanged.

For example, a synthetic quarterly email “We will send the Q3 deck on Friday” is labeled as a promise, with an illustrative acknowledgment and an obligation that remains open. The live Friday/date, recipient and whether acknowledgment is permitted still come from current context and policy. A retrieved example cannot prove that the current deck was delivered or authorize a reminder schedule. This example is teaching material, not a new business rule resolving D-12/D-13.

```mermaid
%%{init: {"theme": "base", "themeVariables": {"fontFamily": "Arial, sans-serif", "fontSize": "15px", "primaryTextColor": "#172033", "edgeLabelBackground": "#ffffff"}, "flowchart": {"htmlLabels": false, "curve": "linear", "nodeSpacing": 35, "rankSpacing": 45}}}%%
flowchart TB
  subgraph RELEASE[Review and release]
    AM[AM selects and de-identifies an example]
    REVIEW[Domain review and Git change]
    CI[Schema checks and holdout evaluation]
    BUNDLE[Immutable prompt and example bundle]
  end
  subgraph WORKER[Workflow and agent worker]
    SELECT[In-process example selector]
    CTX[Trusted current email and case context]
    ANALYZE[Select analysis examples and analyze]
    KNOW[Retrieve current facts and SOP evidence]
    WRITE[Select writing examples and draft]
    CHECK[Independent validation and action policy]
    COMMAND[Persist authorized delivery command]
  end
  HUMAN[Existing portal review]
  DELIVERY[Existing delivery worker and final recheck]
  AM --> REVIEW
  REVIEW --> CI
  CI --> BUNDLE
  CTX --> ANALYZE
  ANALYZE --> KNOW
  KNOW --> WRITE
  WRITE --> CHECK
  CHECK -->|If review required| HUMAN
  HUMAN -->|Approve or edit: recheck| CHECK
  CHECK -->|All applicable gates pass| COMMAND
  BUNDLE -->|Load once by pinned digest| SELECT
  SELECT -->|Classification examples| ANALYZE
  SELECT -->|Writing examples| WRITE
  COMMAND --> DELIVERY
  HUMAN -.->|Manually curate a correction| AM

  classDef default fill:#f8fafc,stroke:#94a3b8,color:#172033,stroke-width:1.5px
  classDef content fill:#eff6ff,stroke:#2563eb,color:#1e3a8a,stroke-width:2px
  classDef examples fill:#f5f3ff,stroke:#7c3aed,color:#4c1d95,stroke-width:2px
  classDef evidence fill:#fffbeb,stroke:#b45309,color:#78350f,stroke-width:2px
  classDef core fill:#ccfbf1,stroke:#0f766e,color:#134e4a,stroke-width:3px,font-weight:bold
  classDef delivery fill:#ffedd5,stroke:#c2410c,color:#7c2d12,stroke-width:3px,font-weight:bold
  class AM,REVIEW,CI,CTX,HUMAN content
  class BUNDLE,SELECT,ANALYZE,WRITE examples
  class KNOW evidence
  class CHECK,COMMAND core
  class DELIVERY delivery
  style WORKER fill:#f0fdfa,stroke:#0f766e,stroke-width:2px
  linkStyle 0,1,2,3 stroke:#2563eb,stroke-width:2px,color:#1e40af
  linkStyle 4,5 stroke:#b45309,stroke-width:2.5px,color:#92400e
  linkStyle 6,9 stroke:#0f766e,stroke-width:4px,color:#115e59
  linkStyle 7,8 stroke:#0369a1,stroke-width:2px,color:#075985
  linkStyle 10,11,12 stroke:#7c3aed,stroke-width:2.5px,color:#5b21b6
  linkStyle 13 stroke:#c2410c,stroke-width:4px,color:#9a3412
  linkStyle 14 stroke:#64748b,stroke-width:1.5px,stroke-dasharray:5 4,color:#475569
```

Blue = preparation/context; violet = illustrative examples; amber = current evidence; thick teal/orange = validation, authorization and delivery. The selector is a function in the worker, not a fifth service. The dashed feedback arrow is a manual content-review loop, never automatic learning. Delivery still performs the full freshness and approval recheck described below.

**Acceptance and extension:** T21.1 defines the clean tuning/holdout split; T11.1 adds bundle loading and selection; T11.2 proves the actual stage handoff; T13.1 proves example-assisted drafting; T13.2 continues to reject unsupported final claims; T21.2 compares baseline and candidate quality. Test wrong-intent/language selection, unknown workflow, zero matches, token overflow, unavailable bundles, copied illustrative facts and holdout leakage. Extend an existing scenario by adding a reviewed record; a new workflow still follows the existing workflow-type/schema process. Consider semantic example search only when measured selection failures in a larger library justify it. AI Search remains for authoritative knowledge and is not a prerequisite for loading examples.

### Records and concurrency

| Record | Authority and invariants |
| --- | --- |
| Account/program facts | CRM master data with source version/time; AAM caches explicitly |
| Case/workstream/obligation | PostgreSQL state/version; unique business-cycle key; completion evidence per approved rule |
| Proposed obligation | Separate pending/accepted/rejected record; promotion decision/idempotency; no implicit active deadline |
| Wait/timer | PostgreSQL due/version/status; scheduler history cannot recreate canceled business state |
| Message/source link | Mailbox + immutable ID, direction, human/AAM/unknown attribution, original hash, folder observations, scoped many-to-many links |
| Run/work ledger | Stable business work key, technical attempts, recovery epoch, pinned versions and output reference |
| Approval | Exact rendered message hash; server-built dependency set; actor authority, policy version, expiry and acknowledged warnings |
| Command/attempt | Unique business effect key; authorization; claim/fence; provider draft/stamp; prepared/submitting/submitted/failed/uncertain states |
| Event/inbox/outbox | Per-case sequence, causation, received/observed times, schema and deduplication; one transaction with state changes |

DB row locking/optimistic versions serialize transitions per case/obligation. DTS ordering is not relied on. Approval dependencies use specific request/obligation/fact versions, not blanket equality to the entire case version. Request disposition changes (including correlated human replies) invalidate pending responses. Every authorization and dispatch reads current control state.

Before dispatch, synchronize required observation scopes through a recorded freshness barrier and re-evaluate the command. Stale intake, uncertain correlation, revoked scope or changed content/dependencies holds it. An external human can still send after that barrier or after provider dispatch starts. Record this unavoidable race, expose lag and reconcile later; do not claim global atomicity with Outlook. Cadence effects of a human follow-up are configurable business rules, not inferred completion.

### Mail delivery and reconciliation

1. Claim a command under a bounded lease/fence and persist a prepared attempt. Do not reclaim a possibly submitting attempt for a fresh send.
2. Create a stamped draft; persist its immutable ID before dispatch. If draft creation times out, query the stamp; duplicates, absence or provider uncertainty require reconciliation, not blind creation-and-send.
3. Re-fetch draft content/recipients/attachments and recheck against approval, observation watermark, business dependencies, expiry and identity restrictions. Hold if a person modified the provider draft. A residual external edit race exists; use a controlled sending mailbox and qualify permissions and behavior.
4. Persist submitting state, send by draft ID, record HTTP acceptance separately. Reconcile by immutable ID/stamp and Sent Items; delayed availability is not failure. A Sent Items copy proves submission, not recipient delivery or business completion.
5. Link NDRs using provider identifiers and trusted correlation evidence. Ambiguous NDRs go to review. Record failure on the communication, preserving history; policy decides escalation, never infer obligation satisfaction.

[Graph documents the draft-to-Sent Items immutable-ID pattern](https://learn.microsoft.com/en-us/graph/outlook-immutable-id). IDs stay stable within the mailbox, with documented exceptions such as archive/export boundaries; these need reconciliation, not universal uniqueness assumptions. `internetMessageId` is secondary correlation only. T15.2 must qualify stamp lookup, lost create/send responses, delayed Sent Items and NDR handling in the target tenant before rollout.

### Workflow packages and story fit

Renewal scanning, quarterly scheduling and program-change triggers all create cases/obligations through the same transitions. Renewal keeps country inputs and term versions; quarterly tracks local deck delivery; program changes track notices and next actions. Whether an obligation is observable, what confirms completion, who approves release, and what consolidation means remain D-12/D-13/D-17 inputs. Synthetic policies permit engineering tests without representing business sign-off. Missing required inputs block activation of the affected workflow. An evidence-policy slot must resolve to an approved registered source/confirmation adapter with declared observable fields and actor scope; an arbitrary label such as "accepted evidence" is insufficient. Engineering validates the configured source contract, while the owner decides whether its evidence is sufficient.

Workflow type versions and client parameter versions are separately published and pinned. Validate compatibility and decision completeness before activation. Keep actual definitions, prompt/schema bundles and compatible images by digest, not just hashes. Retain interpreter compatibility for active types or migrate explicitly with approval/timer impact analysis; short DTS runs reduce replay debt but do not eliminate domain compatibility obligations. Parameter publication cannot create steps or permissions. US-09–US-15 remain separate future changes; the synthetic health-talk extension is a compatibility test, not a prerequisite to initial journeys.

### Security, platform operation and restore

Use Entra for user access and workload identity for managed services. Map Graph workloads to least-privilege Exchange application roles: intake read; delivery draft-write and send; no model identity has mail privileges. Use Exchange RBAC scopes and remove overlapping unscoped Entra grants for those permissions. Test actual allowed/denied Graph calls as well as the RBAC test command, because the latter excludes Entra grants. Record effective grants and propagation/refresh evidence. Non-production worker allowlists plus an Exchange transport rule independently restrict destinations. [Microsoft documents additive permission grants](https://learn.microsoft.com/en-us/exchange/permissions-exo/application-rbac).

Relevance and monitored mailbox selection remain business inputs. Engineering enforces policy before durable content retention/model/index use; metadata-only holding is a possible configured disposition, not a silently imposed relevance algorithm. Unknown attachment classification denies downstream model/search use. Retention and deletion apply to DB, Blob, Search, logs, DTS payloads and backups; carry minimal references in execution history.

Use Key Vault for residual secrets, ACR immutable image digests, Monitor/App Insights correlation and protected diagnostics. Private endpoints where supported and restricted outbound connectivity coexist with outbound Graph polling. No Graph webhook ingress is deployed initially. T01.3 provisions isolated dev/test resources and test-mailbox grants before provider-dependent work. T23 hardens production, including approved region/SKU availability, HA/backup, image scanning, supported AKS release/patch cadence and maintenance ownership.

Bound per-provider concurrency and admission, honor [Graph Retry-After guidance](https://learn.microsoft.com/en-us/graph/throttling), monitor age/throughput and shed optional work before losing required events. Configure HPA/KEDA from an explicitly qualified backlog metric (DB work age/count or supported DTS metric), not an assumed Service Bus queue. PodDisruptionBudgets, termination grace and activity checkpoint/idempotence tests cover node drains. Rate limits, Foundry TPM, mailbox send limits and replica budgets are D-09 capacity inputs. Shutdown stops new claims; abandoned/uncertain attempts remain durable for safe recovery.

Restore runbook, in order:

1. Disable admissions/dispatch and revoke or fence old workers at the infrastructure/identity boundary. Restore PostgreSQL while keeping old execution unable to write or send. Create a new recovery epoch and DTS task hub; do not attach old orchestrations to restored state.
2. Reconcile DB Blob references against retained immutable objects and package artifacts. Missing sources suspend affected cases. Apply deletion/retention policy rather than resurrecting deleted data.
3. Rebuild authorized Search indexes from the restored approved registry and Blob; keep model/knowledge paths unavailable until consistent.
4. Rebuild timers, waits and work-ledger entries from DB business state. Duplicate wake-ups still recheck version/epoch. Reset/reconcile folder cursors conservatively and observe inbound and sent mail since the recovery point.
5. Reconcile provider drafts/sends, including commands lost from the restored DB. Use provider stamps, Sent Items and retained attempt/audit exports where available; unknown outcomes create recovery holds. No provider visibility means no safe automated replay. Never treat absence in the restored DB as proof no send occurred.
6. Verify scope, policies, artifacts, data references and recovered work. Authorize reopening per workload/category only after recovery checks pass; retain unresolved holds. Test this sequence in T23.2, including stale workers and a post-backup accepted send.

## Risks / Trade-offs

- DB timer sweeping adds application logic → indexed due rows, leases, missed-sweep recovery, bounded transactions and concurrency tests.
- Short DTS execution still has replay/retry constraints → pinned technical versions, work ledger, watchdog and idempotent effects; no “disposable” external sends.
- Model validation can miss semantics → independent extraction, conservative span checks, corpus-driven tuning, human review and no claim of perfect detection.
- Polling cannot eliminate simultaneous human/AAM sends → monitored scopes, freshness barriers, visible lag and an explicit race boundary.
- AI Search adds derived-state operations → delay it until needed; authorize current evidence after retrieval; index deletion/restore tests.
- Business policy gaps block production behavior → explicit owner questions and activation validation; no invented answers or claim of business acceptance.

## Migration Plan

Build foundations, scoped test environment and seed evaluation corpus early. Implement DB waits/policy primitives and review-assisted deterministic quarterly local reporting, then independent renewal/program-change and intelligence branches. CRM write-back, extension migration and full model response generation do not block quarterly delivery. Complete composed fault tests and platform hardening before production qualification; automation gates depend on approved category, not calendar time.

Rollback disables new automatic authorizations, fences incompatible workers and preserves the event/command ledger. Prefer compatible forward DB migrations. Restore never rewinds the provider; follow the reconciliation runbook. Importing existing operational data requires a separate inventory, authorization and migration change.

## Open Questions and activation prerequisites

[Decision register](../../../docs/product/decisions.md) distinguishes business questions, technical qualification inputs and selected architecture. Business answers that change observable behavior require a spec update before the affected implementation/activation, not a hidden default. Fixture-only development may proceed against clearly labeled synthetic policies. CI host, regions and quota inventory must be settled at their named task gates; no GitHub/Azure DevOps choice is assumed.

## References

- [Durable Task Python SDK and managed backend](https://learn.microsoft.com/en-us/azure/durable-task/sdks/durable-task-overview); Python listed GA when checked 2026-10-05, not target-region qualification.
- [Orchestration versioning](https://learn.microsoft.com/en-us/azure/durable-task/common/durable-orchestration-versioning).
- [Product scope and assumptions](../../../docs/product/sources.md).
