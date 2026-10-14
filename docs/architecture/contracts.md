# AAM interface and data contracts

Status: proposed logical contracts. Concrete OpenAPI/JSON Schemas and handlers are future task deliverables. Contracts describe authority and failure behavior; they are not deployed endpoints.

## Interfaces

| Surface | Contract |
| --- | --- |
| Folder delta adapter | Poll approved folders; immutable-ID preference; persist message/inbox/outbox and page cursor atomically; classify direction, provenance, relevance and data-use disposition under approved policy |
| Case query/history | Authorize before lookup; return versions, causal sequence, summary freshness and intake health; paginate stably |
| Case/proposal commands | Stable command ID, expected aggregate version, actor scope and configured business rule; conflict/absent policy returns explicit failure |
| Approval API | Exact rendered content plus complete server-generated dependency set; expiry and authority; edited content gets a new validation report and approval |
| Workflow definition/parameters | Publish AM-authored SOP, registered connection/tool bindings and output predicates within engineering-owned capability schemas; version client parameters separately; pin both and reject missing policy |
| Connections | Integration administrators register approved endpoints, adapters, auth references and operation grants; AMs select existing permitted bindings; revocation is enforced live |
| Operator repair | Scoped operation/reason; preserve business event/command ID; current policy and recovery epoch apply |
| CRM adapter | Read master fields with source version/time; optional write-back separately authorized with stable effect key |

No initial public Graph webhook. No generic unauthenticated agent-run API. A client cannot select arbitrary tool permissions, model endpoints or retrieval scopes.

## Event, work and context records

Events carry `event_id`, type, schema version, occurred/observed times, correlation/causation, authorized scope, aggregate/version and payload reference/hash. Unresolved mail stays mailbox-scoped. Case sequence is allocated at DB commit, independent of timestamps. New families include `mail.observed` (inbound/outbound), `proposal.obligation`, `timer.due`, `approval.invalidated`, `command.outcome` and `knowledge.revision`. Unknown major schemas fail; minor fields cannot weaken checks.

The work ledger binds a stable logical work key to recovery epoch, bounded attempt, owner and fencing token, lease/deadline and result. Inbox uniqueness includes consumer, producer and business event ID. Retry after attempt-history cleanup still hits retained business deduplication. Results commit only with the current attempt, unexpired lease and recovery epoch; stale workers fail. Timer keys include obligation version; canceled/obsolete timers cannot regain authority on retry. Delivery uses the DB command queue, not a second agent-controlled dispatch path.

A context snapshot contains current message identity/hash, authorized account/case/workstream, ordered history, observed-human-send/request disposition, intake watermark, case version, authoritative fact versions, workflow type/client parameter versions, bundle digest, data-use restrictions and truncation flags. Blob references are authorized server-side. A snapshot is immutable; policy/current-state checks at dispatch are live.

## Connection and readiness records

A `ConnectionDefinition` contains ID/version, adapter kind and schema version, approved endpoint/environment, authentication reference, operation allowlist, owner, scope/data-use policy and health. Credentials are never serialized into agent inputs. Effective access is the intersection of platform, connection, workflow, actor/service and case permissions. The gateway checks arguments and returned scope, including caches and child calls. A `SourceResult` contains connection/version, operation, account/policy/period, source IDs/versions, effective/observed times, field presence, pagination/completeness and explicit ok/not-found/partial/unavailable/denied status. Provider errors cannot become empty successful evidence.

A `WorkflowDefinition` binds goal, trigger/entry predicate, SOP skill content, allowed operations/connection bindings, required outputs, output readiness expressions, completion evidence, reminder policy and resource versions. Predicate expressions use registered typed operators and domain checks, never arbitrary executable code. Publication requires a readable preview, authority checks and complete/missing-input simulation; natural-language output is a proposal until published.

A `ReadinessDecision` contains output contract ID, predicate/workflow versions, account, evidence IDs/hashes/source versions, evaluated time, freshness bounds, each check result, gaps/conflicts and PASS/NEEDS_DATA/CONFLICT/BLOCKED. Inputs come from the server evidence registry. Only PASS authorizes the application to invoke that output's writer with the bound snapshot. Unknown inventory cannot pass a universal collection check. Evidence changes/expiry invalidate the decision; generation, review and dispatch enforce current dependencies. Approved human attestation supplies evidence under a registered rule; an approval alone never waives a missing mandatory input.

Internal analysis/collection may proceed with incomplete inputs. Planner/child prose is not a customer draft. A permitted clarification uses a separate template/output contract, account/recipient scope and action approval. Its existence cannot qualify the blocked substantive output. Business waits end the bounded agent attempt and persist gaps/wake conditions in PostgreSQL. New evidence starts a new attempt with fresh readiness evaluation.

## Specialist outputs

| Output | Required fields and boundary |
| --- | --- |
| Analysis | Intent/questions/entities, proposed obligations with source spans and ambiguity; no authoritative task creation |
| Research | Per-question coverage, registered source IDs/versions/locations, scope/effective dates, retrieval budget status |
| Readiness | Server-evaluated contract/predicate/evidence binding, PASS or explicit gaps/conflicts; cannot be a model-authored authorization |
| Draft | Valid readiness-decision reference, final subject/body, proposed recipient set and attachments, writer claim map, action type; no authority |
| Validation | Independently extracted final-text spans plus deterministic critical spans, mapping gaps, support verdicts, content hash, warnings and blocking reasons |
| Proposal | Snapshot/stage-result references, proposed transitions and draft revision; dependency candidates are completed/validated by application code |

Malformed, empty, interrupted or budget-exhausted outputs do not imply successful coverage. Missing mandatory inputs block substantive drafting before model invocation; unsupported final claims also block automation. Human warning acknowledgments require configured authority and never bypass security/freshness/prohibited-action controls.

## Example bundle and selection

A reviewed example is illustrative data, with stable ID, registered workflow/intent/language, synthetic incoming email/context, expected analysis, optional response, disposition and rationale. Bundle validation checks schema, labels, uniqueness and bounds. The bundle digest includes example content and selection settings; retained immutable contents support retry and rollback. No example-specific database or public API is added.

Selection is an in-process function over a pinned bundle and trusted workflow context (plus validated intent and language for writing). Its result includes ordered example IDs, selector version/settings, count and omission/no-match reasons in existing stage metadata. Unknown-workflow analysis uses balanced demonstrations without depending on predicted intent. Zero matches is valid; missing or corrupt bundle is failure. Example IDs are never evidence IDs and cannot satisfy current factual coverage or authorize transitions. See the design for ingestion, staged selection and limits.

## Approval and delivery command

Approval dependency set: rendered subject/body hash; canonical to/cc/bcc sets; attachment content hashes; request disposition; referenced obligation, term and fact versions; workflow type/parameter revisions; policy version; current approver authority; validation report/hash, permitted warnings and expiry. Include the output contract/readiness decision and all consulted safety facts, not only writer-selected citations. Current readiness must still hold at dispatch. Unrelated case-version changes alone do not invalidate this set. Missing expiry or dependency coverage holds dispatch.

Commands include stable `command_id` and business-effect key, account/case/request scope, action type, approval/policy reference, dependency hash, observation watermark requirement, expiry and recovery epoch. Attempts include lease/fence, provider mailbox, draft immutable ID, command stamp, attempt timestamps and reconciliation state. Persist before provider side effects. Exact result states distinguish prepared, submitting, submitted, failed and uncertain; recipient delivery remains separate.

Create a draft using immutable-ID preference and app-specific single-value extended property command stamp, then persist its ID before sending. Verify stamp lookup/preservation in the tenant before relying on it. A lost draft-create response requires lookup/reconciliation; a missing lookup result is not proof that creation failed. Recheck provider draft content and application policy before send. HTTP acceptance is not delivery. Delayed Sent Items, NDRs, folder/archive boundary changes, duplicate stamps and unknown correlations have explicit reconciliation/hold paths. Provider IDs and stamps are correlation evidence, never incoming authorization.

## Failure taxonomy

Distinguish invalid input, unauthorized scope, missing business policy, ambiguous context, stale observation, unavailable evidence, disallowed data use, unsupported claim, stale approval/dependency, dependency throttling, run interruption and uncertain external outcome. Preserve structured machine code, retryability, correlation and protected diagnostics. Human decisions and ambiguous sends do not enter unlimited technical retry loops.

## Java core and companion execution contract

Java/Spring Boot is authoritative for cases, jobs, connection grants, evidence, readiness, approvals and commands. The Next.js portal is a client; the Deep Agents TypeScript companion has no business DB or provider write access. Shared JSON schemas are authoritative for generated Java/TypeScript DTOs and boundary validation; do not hand-maintain equivalent schema copies. Java computes canonical evidence/content bindings, with explicit null/absent, decimal-string and UTC timestamp rules.

A bounded execution envelope carries operation/attempt ID, fence/recovery epoch, deadline, phase (plan/draft/validate), immutable snapshot/workflow/bundle references and scoped capability. Java alone grants draft phase after PASS; returned candidates bind to that exact invocation and undergo Java checks. Tokens remain outside prompts/logs, are audience/phase/attempt-bound, and expire or revoke when the attempt ends. Java retains signing authority. The companion tool client sends typed requests to Java; the gateway checks current ownership, cancellation, live permissions and budgets on each call. No arbitrary provider URL, SQL or model-provided readiness can replace those checks.

Java owns bounded HTTP calls and durable retries; the companion has no independent queue/callback store. On timeout/restart/response loss, fence the old invocation, conservatively account for uncertain model consumption and reconcile the job before a fresh attempt. Late results and stale tool calls are rejected. No DB transaction is held during model/network execution. See ADR-16 for deployment, authentication and recovery.
