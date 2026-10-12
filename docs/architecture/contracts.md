# AAM interface and data contracts

Status: proposed logical contracts. Concrete OpenAPI/JSON Schemas and handlers are future task deliverables. Contracts describe authority and failure behavior; they are not deployed endpoints.

## Interfaces

| Surface | Contract |
| --- | --- |
| Folder delta adapter | Poll approved folders; immutable-ID preference; persist message/inbox/outbox and page cursor atomically; classify direction, provenance, relevance and data-use disposition under approved policy |
| Case query/history | Authorize before lookup; return versions, causal sequence, summary freshness and intake health; paginate stably |
| Case/proposal commands | Stable command ID, expected aggregate version, actor scope and configured business rule; conflict/absent policy returns explicit failure |
| Approval API | Exact rendered content plus complete server-generated dependency set; expiry and authority; edited content gets a new validation report and approval |
| Workflow type/parameters | Publish code-reviewed type separately from schema-bounded client revision; pin both; missing policy blocks activation |
| Operator repair | Scoped operation/reason; preserve business event/command ID; current policy and recovery epoch apply |
| CRM adapter | Read master fields with source version/time; optional write-back separately authorized with stable effect key |

No initial public Graph webhook. No generic unauthenticated agent-run API. A client cannot select arbitrary tool permissions, model endpoints or retrieval scopes.

## Event, work and context records

Events carry `event_id`, type, schema version, occurred/observed times, correlation/causation, authorized scope, aggregate/version and payload reference/hash. Unresolved mail stays mailbox-scoped. Case sequence is allocated at DB commit, independent of timestamps. New families include `mail.observed` (inbound/outbound), `proposal.obligation`, `timer.due`, `approval.invalidated`, `command.outcome` and `knowledge.revision`. Unknown major schemas fail; minor fields cannot weaken checks.

The work ledger binds a stable logical work key to recovery epoch, bounded attempt, deterministic DTS ID, lease/deadline and result. Inbox uniqueness includes consumer, producer and business event ID. Retry after scheduler history expiry still hits DB idempotency. Timer keys include obligation version; canceled/obsolete timers cannot regain authority on retry. Delivery uses the DB command queue, not a second DTS dispatch path.

A context snapshot contains current message identity/hash, authorized account/case/workstream, ordered history, observed-human-send/request disposition, intake watermark, case version, authoritative fact versions, workflow type/client parameter versions, bundle digest, data-use restrictions and truncation flags. Blob references are authorized server-side. A snapshot is immutable; policy/current-state checks at dispatch are live.

## Specialist outputs

| Output | Required fields and boundary |
| --- | --- |
| Analysis | Intent/questions/entities, proposed obligations with source spans and ambiguity; no authoritative task creation |
| Research | Per-question coverage, registered source IDs/versions/locations, scope/effective dates, retrieval budget status |
| Draft | Final subject/body, proposed recipient set and attachments, writer claim map, action type; no authority |
| Validation | Independently extracted final-text spans plus deterministic critical spans, mapping gaps, support verdicts, content hash, warnings and blocking reasons |
| Proposal | Snapshot/stage-result references, proposed transitions and draft revision; dependency candidates are completed/validated by application code |

Malformed, empty, interrupted or budget-exhausted outputs do not imply successful coverage. Unknown support blocks automation. Human warning acknowledgments require configured authority and never bypass security/freshness/prohibited-action controls.

## Example bundle and selection

A reviewed example is illustrative data, with stable ID, registered workflow/intent/language, synthetic incoming email/context, expected analysis, optional response, disposition and rationale. Bundle validation checks schema, labels, uniqueness and bounds. The bundle digest includes example content and selection settings; retained immutable contents support retry and rollback. No example-specific database or public API is added.

Selection is an in-process function over a pinned bundle and trusted workflow context (plus validated intent and language for writing). Its result includes ordered example IDs, selector version/settings, count and omission/no-match reasons in existing stage metadata. Unknown-workflow analysis uses balanced demonstrations without depending on predicted intent. Zero matches is valid; missing or corrupt bundle is failure. Example IDs are never evidence IDs and cannot satisfy current factual coverage or authorize transitions. See the design for ingestion, staged selection and limits.

## Approval and delivery command

Approval dependency set: rendered subject/body hash; canonical to/cc/bcc sets; attachment content hashes; request disposition; referenced obligation, term and fact versions; workflow type/parameter revisions; policy version; current approver authority; validation report/hash, permitted warnings and expiry. Include all consulted safety facts, not only writer-selected citations. Unrelated case-version changes alone do not invalidate this set. Missing expiry or dependency coverage holds dispatch.

Commands include stable `command_id` and business-effect key, account/case/request scope, action type, approval/policy reference, dependency hash, observation watermark requirement, expiry and recovery epoch. Attempts include lease/fence, provider mailbox, draft immutable ID, command stamp, attempt timestamps and reconciliation state. Persist before provider side effects. Exact result states distinguish prepared, submitting, submitted, failed and uncertain; recipient delivery remains separate.

Create a draft using immutable-ID preference and app-specific single-value extended property command stamp, then persist its ID before sending. Verify stamp lookup/preservation in the tenant before relying on it. A lost draft-create response requires lookup/reconciliation; a missing lookup result is not proof that creation failed. Recheck provider draft content and application policy before send. HTTP acceptance is not delivery. Delayed Sent Items, NDRs, folder/archive boundary changes, duplicate stamps and unknown correlations have explicit reconciliation/hold paths. Provider IDs and stamps are correlation evidence, never incoming authorization.

## Failure taxonomy

Distinguish invalid input, unauthorized scope, missing business policy, ambiguous context, stale observation, unavailable evidence, disallowed data use, unsupported claim, stale approval/dependency, dependency throttling, run interruption and uncertain external outcome. Preserve structured machine code, retryability, correlation and protected diagnostics. Human decisions and ambiguous sends do not enter unlimited technical retry loops.
