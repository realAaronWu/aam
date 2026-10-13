# AAM implementation tasks

Status: all application tasks remain unchecked and **NOT RUN**. This plan has 56 tasks in 26 stable work packages. Section numbers denote milestone order, not work-package IDs. Paths, test files and commands are future deliverables; no application test exists yet.

Each scenario has exactly one **Closes** task responsible for its complete acceptance proof. **Contributes** means partial support only; it does not claim the scenario passed. A closing task must exercise all listed scenarios through its concrete entry point (or composed test harness) and retain final statuses. Every task also needs its own positive/negative checks and the [common gate](../../../docs/testing/strategy.md).

Business gates identify owner decisions needed before the affected production behavior. Engineering may implement contracts with labeled synthetic policies, but cannot count these as business sign-off. Conditional features and unknown decisions must be explicitly split into a later change if omitted from a qualified release; never mark them passed.

## 1. M1 Foundation and early evaluation

- [ ] 1.1 T01.1 Scaffold typed modules and contract tests

  **Milestone:** M1. **Requirements:** AI-01, QA-01, OP-03. **Depends on:** none.

  **Business gates:** none.

  **Deliverables:** Locked TypeScript/Node.js foundation with Next.js portal, separate worker entry points, shared Zod contracts, Vitest and Playwright; preserve useful demo business/UI modules through reviewed generic imports. Add test/typecheck/build scripts and reject empty test discovery. Deep Agents integration follows in T11.1. The CI host is selected through D-15 before provider-specific workflow files are authored. Planned locations: `app/; workers/; src/aam/; contracts/; tests/; package.json; package-lock.json; tsconfig.json`.

  **Positive acceptance:** Clean checkout installs from the lockfile, typechecks, builds and validates a complete typed sample; runtime schemas reject invalid external values.

  **Negative acceptance:** Unknown schema major version, missing required result, empty test discovery or an undeclared non-TypeScript application runtime fails qualification.

  **Closes:** none. **Contributes:** AI-01-P, AI-01-N, OP-03-P, OP-03-N, OP-03-N2, QA-01-P, QA-01-N.

  **Entry point and layer:** contract schema loader / locked clean install (contracts).

  **Verification command (planned):** `npm run test -- tests/contracts/test_foundation.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T01.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.2 T01.2 Establish routine CI and attributable reports

  **Milestone:** M1. **Requirements:** AI-01, QA-01, OP-03. **Depends on:** T01.1.

  **Business gates:** D-15.

  **Deliverables:** Selected CI provider workflow for specs, lint/types and real test discovery; run manifest and failure artifacts. Implement scenario-ownership/traceability checks and challenge missing-owner, duplicate-owner and missing-test mutations. Planned locations: `ci/; selected provider workflow directory; tests/delivery/`.

  **Positive acceptance:** A deliberate passing job reports source revision and executes discovered tests.

  **Negative acceptance:** A deliberately failing negative test fails CI and preserves diagnostics; empty discovery cannot pass.

  **Closes:** none. **Contributes:** AI-01-P, AI-01-N, OP-03-P, OP-03-N, OP-03-N2, QA-01-P, QA-01-N.

  **Entry point and layer:** selected CI workflow dispatch and retained test report (delivery).

  **Verification command (planned):** `Selected CI provider: dispatch the validation workflow at the reviewed commit; download and inspect its run manifest and failing/passing artifacts. Run npm run test -- tests/delivery/test_ci_discovery.test.ts for the local discovery contract.`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T01.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.3 T01.3 Provision isolated dev/test services and identities

  **Milestone:** M1. **Requirements:** OP-03, OP-04. **Depends on:** T01.1.

  **Business gates:** D-05, D-08, D-09, D-15.

  **Deliverables:** Reproducible managed PostgreSQL, Blob, ACR, Key Vault, Monitor and test workload identities for the three TypeScript deployment roles; approved Graph test mailbox scopes. Add Foundry/Search resources when T11/T12 start. Qualify managed database concurrency and failover rather than assuming local fixtures establish provider compatibility. Planned locations: `infra/dev/; docs/runbooks/dev-setup.md; tests/platform/`.

  **Positive acceptance:** A clean checkout deploys to the approved test resource group and allowed mailbox probes succeed; retain resource and grant inventory.

  **Negative acceptance:** Wrong subscription/tenant, unscoped mailbox or unrestricted test recipient fails admission/probe; no production connection is silently substituted.

  **Closes:** none. **Contributes:** OP-03-P, OP-03-N, OP-03-N2, OP-04-P, OP-04-N.

  **Entry point and layer:** dev/test IaC deployment and scoped test-identity probe (platform).

  **Verification command (planned):** `npm run test -- tests/platform/test_dev_environment.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T01.3/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.4 T02.1 Implement scoped user and workload authorization

  **Milestone:** M1. **Requirements:** OP-01, OP-04. **Depends on:** T01.1, T01.3.

  **Business gates:** none.

  **Deliverables:** Entra user/account policy and workload grants including scoped Graph read, draft-write and send roles; inventory overlapping Entra grants and test effective scope, not just RBAC simulation. Planned locations: `src/aam/security/; tests/security/`.

  **Positive acceptance:** Assigned AM reads and reviews its case through the API.

  **Negative acceptance:** Actual unscoped Graph access and cross-account API access are denied; a broad overlapping Entra grant fails configuration qualification.

  **Closes:** none. **Contributes:** OP-01-P, OP-01-N, OP-04-P, OP-04-N.

  **Entry point and layer:** authenticated case API authorization middleware (security).

  **Verification command (planned):** `npm run test -- tests/security/test_identity.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T02.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.5 T03.1 Create business entities and version constraints

  **Milestone:** M1. **Requirements:** CS-01, CS-02, CS-04, WF-01, WF-02, AU-02, AU-03. **Depends on:** T01.1, T01.3, T02.1.

  **Business gates:** none.

  **Deliverables:** Migrations for authoritative cases/obligations, proposed tasks, waits/timers, dependencies, commands/attempts, recovery epoch and technical work ledger. ADR-06 is the schema contract. Planned locations: `src/aam/persistence/; migrations/; contracts/events/`.

  **Positive acceptance:** One business-cycle key persists with versioned records and source references.

  **Negative acceptance:** Duplicate effect/cycle keys, stale versions and obsolete recovery epochs fail deterministically.

  **Closes:** none. **Contributes:** AU-02-P, AU-02-N, AU-02-P2, AU-02-N2, AU-03-P, AU-03-N, AU-03-N2, CS-01-P, CS-01-N, CS-02-P, CS-02-N, CS-04-P, CS-04-N, WF-01-P, WF-01-N, WF-02-P, WF-02-N.

  **Entry point and layer:** database migrations and case/obligation repository transactions (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_persistence.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T03.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.6 T03.2 Implement inbox and transactional outbox

  **Milestone:** M1. **Requirements:** WF-02. **Depends on:** T03.1.

  **Business gates:** none.

  **Deliverables:** Transactional inbox/outbox, stable case sequence, unique durable work rows and atomic outbox-to-work transfer with lost-commit-ack reconciliation; DB polling command queue is the sole delivery authority. Planned locations: `src/aam/persistence/; migrations/; contracts/events/`.

  **Positive acceptance:** Committed state and its event survive worker restart.

  **Negative acceptance:** Fault during outbox transfer or after commit acknowledgement loss neither loses work nor repeats the business transition; querying the stable key distinguishes committed from unfinished work.

  **Closes:** WF-02-N. **Contributes:** WF-02-P.

  **Entry point and layer:** inbox/outbox transaction and work-ledger recovery harness (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_outbox.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T03.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.7 T04.1 Persist folder delta pages and stable mail identities

  **Milestone:** M1. **Requirements:** IN-01. **Depends on:** T02.1, T03.2.

  **Business gates:** D-05, D-08, D-16.

  **Deliverables:** Outbound Graph polling with cursor per approved mailbox/folder, immutable IDs and Blob-first admission followed by atomic message/cursor/outbox DB commit. Planned locations: `src/aam/integrations/mail/; tests/contracts/mail/`.

  **Positive acceptance:** A poll page advances only after every allowed message is recorded; same-mailbox folder movement retains one identity.

  **Negative acceptance:** Blob failure, DB failure after Blob write and duplicate delta entries neither skip the cursor nor duplicate committed work; orphan objects are collectible.

  **Closes:** none. **Contributes:** IN-01-P, IN-01-N.

  **Entry point and layer:** folder delta adapter page commit/cursor boundary (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_mail_delta.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T04.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.8 T05.1 Build bounded inspection and extraction

  **Milestone:** M1. **Requirements:** IN-02. **Depends on:** T04.1.

  **Business gates:** D-08.

  **Deliverables:** Bounded scanning/extraction with source positions, quarantine and data-use classification; no unclassified content is sent to model/search. Planned locations: `src/aam/documents/; tests/documents/`.

  **Positive acceptance:** A supported deck yields traceable text and evidence references.

  **Negative acceptance:** Encrypted, unsafe, oversized or failed extraction becomes quarantine/review without invented content.

  **Closes:** IN-02-P, IN-02-N. **Contributes:** none.

  **Entry point and layer:** attachment admission, scan and extraction pipeline (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_attachments.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T05.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.9 T06.1 Implement authoritative reads and freshness

  **Milestone:** M1. **Requirements:** IN-03. **Depends on:** T02.1, T03.2.

  **Business gates:** D-04.

  **Deliverables:** Versioned Dynamics 365 Sales / Dataverse read adapter, approved environment/table/field mapping and source freshness; provider identity roles plus application account/field filtering. Other Dynamics families require separate adapter qualification. Planned locations: `src/aam/integrations/crm/; tests/contracts/crm/`.

  **Positive acceptance:** Account, owner, contacts and renewal dates retain source time/version.

  **Negative acceptance:** Unavailable or stale mandatory facts block dependent actions.

  **Closes:** IN-03-P. **Contributes:** IN-03-N.

  **Entry point and layer:** CRM synchronization adapter read/freshness boundary (contracts).

  **Verification command (planned):** `npm run test -- tests/contracts/test_crm_read.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T06.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.10 T09.1 Implement conversation links and authorized case edits

  **Milestone:** M1. **Requirements:** CS-01, CS-02. **Depends on:** T02.1, T03.2.

  **Business gates:** none.

  **Deliverables:** Link, split, merge and reassign commands with causal event history. Planned locations: `src/aam/cases/; tests/cases/`.

  **Positive acceptance:** Two threads link to one authorized case and a split preserves original references.

  **Negative acceptance:** Cross-account merge is rejected and equal timestamps do not reverse causal ordering.

  **Closes:** CS-01-P, CS-01-N. **Contributes:** CS-02-P, CS-02-N.

  **Entry point and layer:** case link/split/merge command API (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_case_links.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T09.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.11 T07.1 Resolve identities with evidence and review

  **Milestone:** M1. **Requirements:** CX-01. **Depends on:** T04.1, T06.1, T09.1.

  **Business gates:** D-16.

  **Deliverables:** Candidate matching, identifier checks and ambiguity queue. Planned locations: `src/aam/context/resolver.ts; tests/context/`.

  **Positive acceptance:** One broker with two clients resolves to the explicit authorized policy.

  **Negative acceptance:** Broker or local team ambiguity and explicit multi-client mail without configured handling hold restricted retrieval/actions; no sender-only guess.

  **Closes:** CX-01-P, CX-01-N, CX-01-N2, CX-01-N3. **Contributes:** none.

  **Entry point and layer:** account/workstream resolver and ambiguity queue (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_resolution.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T07.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.12 T04.2 Reconcile inbound and Sent Items observation

  **Milestone:** M1. **Requirements:** IN-01, IN-04. **Depends on:** T04.1, T07.1.

  **Business gates:** D-05, D-13, D-16.

  **Deliverables:** Controlled cursor-reset/rescan, approved-folder reconciliation, Sent Items human/AAM/unknown attribution, request disposition invalidation and observation health. No webhook subscription lifecycle is required initially. Planned locations: `src/aam/integrations/mail/; tests/contracts/mail/`.

  **Positive acceptance:** Catch-up after downtime records each message once, including human replies and provider-stamped AAM sends.

  **Negative acceptance:** Expired cursor, missed move, unknown attribution or stale Sent Items health is visible and cannot silently permit dependent automatic dispatch.

  **Closes:** IN-01-P, IN-01-N. **Contributes:** IN-04-P, IN-04-N.

  **Entry point and layer:** inbound/Sent Items catch-up and request-disposition projection (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_mail_observation.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T04.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.13 T08.1 Freeze and validate stage input snapshots

  **Milestone:** M1. **Requirements:** CX-02. **Depends on:** T07.1.

  **Business gates:** none.

  **Deliverables:** Server-built snapshot with data scope, source hashes, workflow/client parameters and observed-mail freshness; attachments may be pending/unavailable without blocking snapshot construction. Planned locations: `src/aam/context/snapshots.ts; contracts/context/`.

  **Positive acceptance:** Every specialist sees the same authoritative message/account identity.

  **Negative acceptance:** Changed original ID/body or model-supplied broader scope fails before tool execution.

  **Closes:** CX-02-P, CX-02-N. **Contributes:** none.

  **Entry point and layer:** context snapshot builder and specialist-input boundary (contracts).

  **Verification command (planned):** `npm run test -- tests/contracts/test_context_snapshot.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T08.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.14 T08.2 Bound history and represent missing material

  **Milestone:** M1. **Requirements:** CX-03. **Depends on:** T08.1.

  **Business gates:** none.

  **Deliverables:** Ordered context selection and explicit truncation/unavailable flags. Planned locations: `src/aam/context/snapshots.ts; contracts/context/`.

  **Positive acceptance:** Recent complete context remains usable with omitted-history metadata.

  **Negative acceptance:** Truncated current body or unprocessed required attachment cannot yield a fully supported answer.

  **Closes:** CX-03-P, CX-03-N. **Contributes:** none.

  **Entry point and layer:** context selection with unavailable-body/attachment states (contracts).

  **Verification command (planned):** `npm run test -- tests/contracts/test_context_limits.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T08.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.15 T09.2 Implement completion and waiver rules

  **Milestone:** M1. **Requirements:** CS-02. **Depends on:** T09.1.

  **Business gates:** D-07, D-12.

  **Deliverables:** Completion/waiver evaluator with typed evidence-policy slots. Missing approved evidence or waiver rules are explicit activation/closure blocks. Planned locations: `src/aam/cases/; tests/cases/`.

  **Positive acceptance:** Accepted completion evidence or authorized waiver satisfies all mandatory work.

  **Negative acceptance:** Promise, draft, send acceptance and successful agent run cannot alone close a case.

  **Closes:** CS-02-P, CS-02-N. **Contributes:** none.

  **Entry point and layer:** obligation completion and case-closure command handler (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_completion.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T09.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.16 T09.3 Separate obligation proposals and authorized promotion

  **Milestone:** M1. **Requirements:** CS-04. **Depends on:** T09.2.

  **Business gates:** D-16.

  **Deliverables:** Pending proposal storage and idempotent decision handler driven by configured promotion rules, with no implicit model-created timer. Planned locations: `src/aam/cases/proposals/; tests/integration/`.

  **Positive acceptance:** A repeated authorized synthetic-policy decision creates one obligation with provenance.

  **Negative acceptance:** Unset promotion policy or unauthorized owner/date change leaves the proposal pending with no reminder.

  **Closes:** CS-04-P, CS-04-N. **Contributes:** none.

  **Entry point and layer:** proposed-obligation decision command handler (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_proposal_promotion.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T09.3/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.17 T10.1 Build case overview and actionable work queues

  **Milestone:** M1. **Requirements:** CS-03. **Depends on:** T02.1, T09.2.

  **Business gates:** none.

  **Deliverables:** Scoped case list, obligations, evidence timeline, explicit holds and read-only approval status. Approval actions are delivered in T14.2, not prematurely here. Planned locations: `web/; src/aam/api/cases/; tests/ui/; tests/e2e/case-overview.spec.ts`.

  **Positive acceptance:** AM can inspect owner/deadline/evidence and record authorized resolution.

  **Negative acceptance:** Unauthorized cases stay hidden; same-time events preserve causality.

  **Closes:** CS-03-N. **Contributes:** CS-03-P.

  **Entry point and layer:** case query API and evidence timeline browser flow (ui).

  **Verification command (planned):** `npm run test -- tests/ui/test_case_overview.test.ts && npm run test:e2e -- tests/e2e/case-overview.spec.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T10.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.18 T16.1 Implement bounded jobs, leases and business-wait recovery

  **Milestone:** M1. **Requirements:** WF-01, WF-02. **Depends on:** T03.2, T09.2, T01.3.

  **Business gates:** none.

  **Deliverables:** DB-owned waits/timers and bounded Node.js jobs; atomic claims, owner/lease/fencing token, deadline, cancellation, retry budget, quarantine and recovery epoch. A watchdog reclaims expired attempts; result/state/outbox commit together only under current ownership. Planned locations: `workers/; src/aam/jobs/; src/aam/workflows/; tests/integration/`.

  **Positive acceptance:** Reply or approval after restart creates new bounded work against the original DB wait; two worker replicas claim work safely and recover a lost commit acknowledgement without duplicate committed results.

  **Negative acceptance:** Missing handler version suspends the case; expired lease, cancelled attempt or old epoch cannot commit late results; attempt-history cleanup preserves business deduplication; exhausted retries quarantine rather than loop indefinitely.

  **Closes:** WF-01-P, WF-01-N. **Contributes:** WF-02-P, WF-02-N.

  **Entry point and layer:** PostgreSQL job claim/commit, lease watchdog and DB-owned wait recovery (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_bounded_execution.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T16.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.19 T16.2 Implement calendars timers and operator transitions

  **Milestone:** M1. **Requirements:** WF-03, WF-02. **Depends on:** T16.1.

  **Business gates:** D-06, D-14.

  **Deliverables:** DB due sweep, holiday/timezone calculation, reschedule/pause/cancel, missed-sweep recovery and configurable upcoming/overdue notification events. Planned locations: `src/aam/workflows/; tests/workflows/`.

  **Positive acceptance:** Synthetic approved holiday, reschedule and notification rules emit one expected occurrence; superseded timers do not dispatch.

  **Negative acceptance:** Missing owner/calendar/channel/rule produces an explicit hold; two sweepers cannot activate the same timer version twice.

  **Closes:** WF-03-P, WF-03-N, WF-03-P2. **Contributes:** WF-02-P, WF-02-N.

  **Entry point and layer:** due-row sweep and current-version transition handler (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_timers.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T16.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.20 T17.1 Validate and publish versioned packages

  **Milestone:** M1. **Requirements:** CFG-01, AI-05. **Depends on:** T16.1.

  **Business gates:** D-06, D-07, D-12, D-13, D-16, D-17.

  **Deliverables:** Registered capability schemas, versioned workflow-definition registry and common typed predicate evaluator; immutable artifacts, case pinning, readiness/completion contracts and mandatory policy slots. Supports deterministic templates without requiring the model runtime. Planned locations: `resources/workflows/; src/aam/configuration/; tests/configuration/`.

  **Positive acceptance:** New case pins version two while active version one remains reproducible; complete template inputs pass their output predicate.

  **Negative acceptance:** Missing completion/readiness contracts, undefined fields, empty required-input sets, arbitrary predicate code, unregistered tools or incompatible schema block publication; unknown inputs cannot evaluate true.

  **Closes:** CFG-01-P, CFG-01-N. **Contributes:** AI-05-P, AI-05-N, AI-05-P2, AI-05-N2.

  **Entry point and layer:** workflow-type registry publish/activation API (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_workflow_registry.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T17.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.21 T17.3 Publish bounded client parameter revisions

  **Milestone:** M1. **Requirements:** CFG-03. **Depends on:** T17.1.

  **Business gates:** D-06, D-13, D-16.

  **Deliverables:** Authorized typed parameter API, publication audit and pinning; distinct from engineering-owned type/permission schemas. Planned locations: `src/aam/configuration/; web/configuration/; tests/integration/; tests/e2e/client-parameters.spec.ts`.

  **Positive acceptance:** Approved cadence revision affects new cases only; previous revision remains retrievable.

  **Negative acceptance:** Parameter publication cannot inject a step/tool/permission or silently mutate an active case.

  **Closes:** CFG-03-P, CFG-03-N. **Contributes:** none.

  **Entry point and layer:** client-parameter publish and case-version pinning API (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_client_parameters.test.ts && npm run test:e2e -- tests/e2e/client-parameters.spec.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T17.3/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.22 T21.1 Create labeled representative evaluation sets

  **Milestone:** M1. **Requirements:** QA-01. **Depends on:** T01.1.

  **Business gates:** D-08.

  **Deliverables:** Early synthetic seed corpus, versioned labels and frozen holdout; domain-review and data-use approval process for de-identified real samples and small runtime demonstration sets. Split by source conversation/scenario family before tuning; exclude held-out examples from shipped bundles and annotate missing slices. Planned locations: `evals/; tests/evaluation/`.

  **Positive acceptance:** All three journeys, languages and broker ambiguity slices have positive/adverse examples; reviewed runtime demonstrations and held-out records have disjoint identities, content hashes and source conversation groups.

  **Negative acceptance:** Unapproved real data, unreviewed examples, exact or related-conversation leakage, or absent rare-category samples prevents the corresponding evaluation claim; a live reply approval alone cannot publish an example.

  **Closes:** none. **Contributes:** QA-01-P, QA-01-N.

  **Entry point and layer:** corpus manifest/label loader and dataset approval checks (evaluation).

  **Verification command (planned):** `npm run test -- tests/evaluation/test_corpus.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T21.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 1.23 T22.1 Expose correlated health metrics and exceptions

  **Milestone:** M1. **Requirements:** OP-02. **Depends on:** T03.2, T01.3.

  **Business gates:** none.

  **Deliverables:** Early DB work-age/queue metrics, correlation, persistence readiness and safe diagnostics; add provider/model metrics with later integrations. Planned locations: `src/aam/operations/; docs/runbooks/; tests/recovery/`.

  **Positive acceptance:** Injected outage produces a case-linked actionable diagnostic.

  **Negative acceptance:** Critical persistence failure cannot remain healthy or silently drop work.

  **Closes:** none. **Contributes:** OP-02-P, OP-02-N.

  **Entry point and layer:** work ledger health/readiness and backlog diagnostic endpoints (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_observability.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T22.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

## 2. M2 Review-assisted deterministic quarterly delivery

- [ ] 2.1 T14.1 Evaluate independent action policy

  **Milestone:** M2. **Requirements:** AU-01, AI-05. **Depends on:** T09.2, T09.3, T04.2, T16.1, T17.3.

  **Business gates:** D-13, D-16.

  **Deliverables:** Shared configurable action/recipient/authority policy with explicit missing-policy holds; deterministic template proposals must first pass the common output-readiness evaluator before render and authorization. Planned locations: `src/aam/authorization/; src/aam/api/approvals/; tests/authorization/`.

  **Positive acceptance:** Approved low-risk policy permits only its scoped action type.

  **Negative acceptance:** Matched knowledge alone cannot authorize terms, commitments or ambiguous accounts; a human approval cannot bypass a missing mandatory template input.

  **Closes:** AU-01-P, AU-01-N. **Contributes:** AI-05-N, AI-05-N2.

  **Entry point and layer:** shared action policy on deterministic proposals (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_action_policy.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T14.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 2.2 T14.2 Bind exact approvals and invalidate stale drafts

  **Milestone:** M2. **Requirements:** AU-02. **Depends on:** T14.1, T10.1.

  **Business gates:** D-13.

  **Deliverables:** Exact-content approval API/UI for deterministic proposals, server-built dependency sets and expiry; DB-owned waiting state survives restarts. Planned locations: `src/aam/authorization/; src/aam/api/approvals/; tests/authorization/; tests/e2e/approval-dependencies.spec.ts`.

  **Positive acceptance:** Unchanged approved content passes; an unrelated country event does not invalidate it; restart retains the pending approval.

  **Negative acceptance:** Changed content/recipient, referenced term, request disposition, policy/authority or expiry invalidates approval and blocks dispatch.

  **Closes:** none. **Contributes:** AU-02-P, AU-02-N, AU-02-P2, AU-02-N2.

  **Entry point and layer:** approval API/UI and dispatch dependency-set validator (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_approval_dependencies.test.ts && npm run test:e2e -- tests/e2e/approval-dependencies.spec.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T14.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 2.3 T15.1 Execute scoped authorized commands

  **Milestone:** M2. **Requirements:** AU-03, WF-02, AU-02. **Depends on:** T04.2, T14.2, T16.2.

  **Business gates:** none.

  **Deliverables:** Leased/fenced DB command queue, shared dependency recheck, observation barrier and draft-create/persist-ID/send flow with scoped identity and independent test recipient guards. Planned locations: `src/aam/delivery/; tests/delivery/`.

  **Positive acceptance:** Approved current content is submitted and stored with its exact hash.

  **Negative acceptance:** A completed/canceled obligation, stale observation, duplicate command, changed provider draft or stale approval cannot issue a fresh send; crash in submitting enters uncertainty.

  **Closes:** AU-02-P, AU-02-N, AU-02-P2, AU-02-N2, WF-02-P. **Contributes:** AU-03-P, AU-03-N, AU-03-N2, WF-02-N.

  **Entry point and layer:** DB command claim, draft creation and Graph sandbox dispatch (provider).

  **Verification command (planned):** `npm run test -- tests/provider/test_delivery.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T15.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 2.4 T15.2 Reconcile uncertain outcomes and failures

  **Milestone:** M2. **Requirements:** AU-03, IN-04, OP-04. **Depends on:** T15.1.

  **Business gates:** D-05, D-13, D-16.

  **Deliverables:** Tenant qualification of immutable draft IDs and app-specific stamp create/filter/sent preservation; lost create/send response reconciliation, late NDR mapping, effective mailbox scope and transport-rule probes. Planned locations: `src/aam/delivery/; tests/delivery/`.

  **Positive acceptance:** A lost send response is reconciled to the same command and a human reply observed before dispatch suppresses its pending draft.

  **Negative acceptance:** Unknown/duplicate stamp, delayed Sent Items, ambiguous NDR or unscoped mailbox produces an explicit hold/denial; no blind resend. Bypass the worker allowlist in a controlled test to verify the independent provider restriction.

  **Closes:** AU-03-P, AU-03-N, AU-03-N2, IN-04-P, IN-04-N, OP-04-P, OP-04-N. **Contributes:** none.

  **Entry point and layer:** Graph reconciliation and stale-observation dispatch barrier (provider).

  **Verification command (planned):** `npm run test -- tests/provider/test_reconciliation.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T15.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 2.5 T19.1 Create quarterly country obligations

  **Milestone:** M2. **Requirements:** QD-01. **Depends on:** T17.3, T09.2, T16.2.

  **Business gates:** D-06.

  **Deliverables:** Account/quarter/country scheduling, contacts and reminder rules. Planned locations: `resources/workflows/quarterly-delivery/; tests/journeys/quarterly/`.

  **Positive acceptance:** Required countries receive one owned obligation each.

  **Negative acceptance:** Repeated schedule or unknown local contact creates no duplicate or unsafe recipient.

  **Closes:** QD-01-P, QD-01-N. **Contributes:** none.

  **Entry point and layer:** quarterly schedule-to-case command (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_quarterly_schedule.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T19.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 2.6 T19.2 Evaluate deck delivery evidence

  **Milestone:** M2. **Requirements:** QD-02. **Depends on:** T19.1, T15.2, T05.1.

  **Business gates:** D-07, D-12, D-13.

  **Deliverables:** Review-assisted deterministic template reminders and evidence acceptance under explicit synthetic policies; no full LLM, CRM write-back or type-migration dependency. Planned locations: `resources/workflows/quarterly-delivery/; tests/journeys/quarterly/`.

  **Positive acceptance:** Valid delivery confirmation completes that country and stops its reminders.

  **Negative acceptance:** Promise or wrong-quarter deck keeps the obligation outstanding.

  **Closes:** QD-02-P, QD-02-N. **Contributes:** none.

  **Entry point and layer:** quarterly reminder/evidence/closure journey using approved templates (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_quarterly_delivery.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T19.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

## 3. M3 Dynamic mail intelligence and remaining priority journeys

- [ ] 3.1 T11.1 Adapt registry and bounded specialist invocation

  **Milestone:** M3. **Requirements:** AI-01. **Depends on:** T08.2, T21.1, T01.3.

  **Business gates:** none.

  **Deliverables:** Locked Deep Agents TypeScript/LangChain/LangGraph adapter with Zod schemas, reviewed skills, shared model/tool/time budgets including child and summarization calls, cancellation propagation and restricted effective tools. Versioned example loader/selector retains immutable bundle bytes. Disable shell, arbitrary SQL/network, writable SOP memory and unrestricted writer delegation. Planned locations: `src/aam/agents/; contracts/; resources/agents/; resources/examples/; tests/agents/`.

  **Positive acceptance:** Pinned runtime loads the selected skill and reproducible examples, invokes approved typed tools within aggregate budgets, and exposes the tested restricted capabilities to every child.

  **Negative acceptance:** Missing/corrupt bundle, unknown labels, hidden default tools, child permission widening, budget overshoot, swallowed cancellation, malformed output and timeout fail explicitly; examples never displace mandatory current context.

  **Closes:** none. **Contributes:** AI-01-P, AI-01-N.

  **Entry point and layer:** Deep Agents adapter, skill loader and effective main/child tool inventory (agents).

  **Verification command (planned):** `npm run test -- tests/agents/test_model_adapter.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T11.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.2 T10.2 Build versioned case summaries

  **Milestone:** M3. **Requirements:** CS-03. **Depends on:** T10.1, T11.1.

  **Business gates:** none.

  **Deliverables:** Event-triggered summary job and freshness display. Planned locations: `web/; src/aam/api/cases/; tests/ui/; tests/e2e/summary-freshness.spec.ts`.

  **Positive acceptance:** Committed structured changes appear while a narrative refresh is pending.

  **Negative acceptance:** A stale summary is labeled and cannot overwrite newer case facts.

  **Closes:** CS-03-P. **Contributes:** CS-03-N.

  **Entry point and layer:** summary generation job plus case-view freshness presentation (ui).

  **Verification command (planned):** `npm run test -- tests/ui/test_summary_freshness.test.ts && npm run test:e2e -- tests/e2e/summary-freshness.spec.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T10.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.3 T12.1 Implement approved scoped retrieval

  **Milestone:** M3. **Requirements:** AI-02. **Depends on:** T08.1, T01.3.

  **Business gates:** none.

  **Deliverables:** Approved knowledge registry/indexer and scoped API/Search adapter with source version, effective-date, completeness and DB authorization revalidation. Evidence registry rejects unknown IDs and conflicting content for an existing ID; search top-k does not establish exhaustive business evidence. Planned locations: `src/aam/retrieval/; tests/retrieval/`.

  **Positive acceptance:** Applicable evidence for two questions is returned with source locations.

  **Negative acceptance:** Model-supplied filters cannot retrieve another client or unpublished/expired SOP; fabricated evidence IDs and collisions with different source contents are rejected.

  **Closes:** none. **Contributes:** AI-02-P, AI-02-N.

  **Entry point and layer:** knowledge indexer and scoped Search adapter (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_retrieval.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T12.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.4 T18.1 Create proactive regional and country cases

  **Milestone:** M3. **Requirements:** RN-01. **Depends on:** T06.1, T17.3, T16.2.

  **Business gates:** D-04, D-06.

  **Deliverables:** CRM eligibility scan with calendar-month and account-specific lead-time rules. Planned locations: `resources/workflows/renewal/; tests/journeys/renewal/`.

  **Positive acceptance:** Configured earlier jumbo date creates one cycle and applicable country workstreams.

  **Negative acceptance:** Repeated scan or missing renewal date creates no duplicate or invented schedule.

  **Closes:** RN-01-P, RN-01-N. **Contributes:** none.

  **Entry point and layer:** CRM eligibility sweep and renewal case creation (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_renewal_schedule.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T18.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.5 T18.2 Coordinate pricing terms and release

  **Milestone:** M3. **Requirements:** RN-02. **Depends on:** T18.1, T15.2, T05.1.

  **Business gates:** D-12, D-13, D-17.

  **Deliverables:** Versioned pricing/terms and approved consolidation interface. Scope is gated by D-17; do not implement numeric authorship or choose document-only behavior as an unstated business answer. Planned locations: `resources/workflows/renewal/; tests/journeys/renewal/`.

  **Positive acceptance:** Accepted applicable country terms produce an approvable consolidation.

  **Negative acceptance:** Missing business consolidation scope, stale input versions or incomplete approved preconditions hold release; no inferred exception.

  **Closes:** RN-02-P, RN-02-N. **Contributes:** none.

  **Entry point and layer:** pricing/terms version acceptance and release command boundary (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_renewal_terms.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T18.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.6 T18.3 Track negotiation rounds and finalization

  **Milestone:** M3. **Requirements:** RN-03. **Depends on:** T18.2.

  **Business gates:** D-12, D-17.

  **Deliverables:** Round/action history, confirmation evidence and obsolete-reminder cancellation. Planned locations: `resources/workflows/renewal/; tests/journeys/renewal/`.

  **Positive acceptance:** Confirmed agreed outcome finalizes the correct versions.

  **Negative acceptance:** Discount request or acknowledgment cannot accept commercial terms or close the renewal.

  **Closes:** RN-03-P, RN-03-N. **Contributes:** none.

  **Entry point and layer:** negotiation event processing and finalization command (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_renewal_finalization.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T18.3/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.7 T20.1 Resolve underwriting notice and affected entities

  **Milestone:** M3. **Requirements:** PC-01. **Depends on:** T06.1, T07.1, T17.3.

  **Business gates:** D-17.

  **Deliverables:** Authenticated notice intake, existing/new-opportunity mapping and uncertainty handling. Planned locations: `resources/workflows/program-change/; tests/journeys/program-change/`.

  **Positive acceptance:** Confirmed notice creates linked impacted workstreams.

  **Negative acceptance:** A lookalike sender, conflicting entities or missing business authority policy holds the trigger; passing mail authentication alone cannot establish underwriting authority.

  **Closes:** PC-01-P, PC-01-N. **Contributes:** none.

  **Entry point and layer:** program-change notice intake with configured authority policy (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_program_trigger.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T20.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.8 T20.2 Coordinate approved notices and next actions

  **Milestone:** M3. **Requirements:** PC-02. **Depends on:** T20.1, T15.2.

  **Business gates:** D-12, D-13.

  **Deliverables:** Approval path, acknowledgment tracking and evidence-based closure. Planned locations: `resources/workflows/program-change/; tests/journeys/program-change/`.

  **Positive acceptance:** Approved communication and completed agreed actions allow closure.

  **Negative acceptance:** Replacement coverage promise is blocked; sending alone cannot close the case.

  **Closes:** PC-02-P, PC-02-N. **Contributes:** none.

  **Entry point and layer:** approved notice and next-action closure journey (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_program_actions.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T20.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.9 T06.3 Register connections and enforce scoped agent reads

  **Milestone:** M3. **Requirements:** CX-04. **Depends on:** T06.1, T04.2, T08.1, T12.1.

  **Business gates:** D-04, D-05, D-08.

  **Deliverables:** Connection administration, credential references, operation catalogue, adapter/MCP wrapper and revocable per-call scope checks; normalized evidence envelope and scoped cache. Validate D365, Outlook and knowledge adapters in isolated provider environments. Planned locations: `src/aam/connections/; src/aam/integrations/; tests/integration/`.

  **Positive acceptance:** Approved main/child reads return only permitted account fields with provenance; complete paginated results retain membership evidence and provider access probes succeed.

  **Negative acceptance:** Revoked grants, cross-account cache hits, forged arguments, partial pages, failed refreshes, changed MCP schemas or child write attempts hold/deny rather than yield complete evidence; no secrets enter prompts/logs.

  **Closes:** CX-04-P, CX-04-N, CX-04-P2, CX-04-N2. **Contributes:** none.

  **Entry point and layer:** connection registration API and guarded read gateway including provider qualification (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_connections.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T06.3/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.10 T11.2 Run dynamic SOP planning with mandatory application gates

  **Milestone:** M3. **Requirements:** AI-01, CS-04. **Depends on:** T11.1, T16.1, T17.1, T06.3.

  **Business gates:** none.

  **Deliverables:** Application-owned immutable context and typed handoff records around dynamic Deep Agents planning; children receive server-built snapshot references and cannot rewrite upstream results. Record evidence/example IDs and proposals; restart bounded attempts from durable state without a second business workflow store. Planned locations: `src/aam/agents/; resources/agents/; tests/agents/`.

  **Positive acceptance:** Different valid evidence conditions produce different permitted collection sequences; both preserve current context, workflow pins, readiness-before-writing and validation-before-authorization.

  **Negative acceptance:** Planner prose, forged readiness, premature writer calls or injected source instructions cannot bypass application boundaries; child outputs cannot mutate scope or directly create obligations.

  **Closes:** AI-01-P, AI-01-N. **Contributes:** CS-04-P, CS-04-N.

  **Entry point and layer:** bounded Deep Agents work handler and guarded output/tool entry points (agents).

  **Verification command (planned):** `npm run test -- tests/agents/test_stage_coordinator.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T11.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.11 T12.2 Implement evidence registration and question coverage

  **Milestone:** M3. **Requirements:** AI-02. **Depends on:** T12.1, T11.2.

  **Business gates:** none.

  **Deliverables:** Evidence registry with versions, scope, provenance, field presence, completeness, coverage states and bounded multi-query retrieval; distinguish source failure, partial results, confirmed absence and a proved complete collection. Planned locations: `src/aam/retrieval/; tests/retrieval/`.

  **Positive acceptance:** Per-question support distinguishes sufficient from partial coverage.

  **Negative acceptance:** Unknown IDs, conflicting evidence and budget exhaustion never become sufficient coverage.

  **Closes:** AI-02-P, AI-02-N. **Contributes:** none.

  **Entry point and layer:** research loop evidence registry and coverage evaluator (agents).

  **Verification command (planned):** `npm run test -- tests/agents/test_research_coverage.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T12.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.12 T13.1 Draft typed responses and internal claim maps

  **Milestone:** M3. **Requirements:** AI-04, AI-05. **Depends on:** T12.2.

  **Business gates:** none.

  **Deliverables:** Application-invoked typed writer guarded by an exact readiness PASS snapshot; distinct current facts and examples, language/continuity checks, rendered message and claim map. Internal planner text cannot become recipient-visible output. Planned locations: `src/aam/drafting/; src/aam/validation/; tests/drafting/`.

  **Positive acceptance:** Relevant intent/language demonstrations guide phrasing; newly supplied information is acknowledged without repeating answered questions; no-match drafting preserves all factual/review controls.

  **Negative acceptance:** Missing, expired or mismatched PASS prevents writer invocation; copied illustrative facts, secrets and unsupported commitments are rejected; parent or child agents cannot directly publish drafts.

  **Closes:** AI-04-P, AI-04-N. **Contributes:** AI-05-P, AI-05-N, AI-05-N2.

  **Entry point and layer:** writer adapter on final language/continuity and disclosure rules (agents).

  **Verification command (planned):** `npm run test -- tests/agents/test_drafting.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T13.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.13 T13.2 Validate factual claims and commitments

  **Milestone:** M3. **Requirements:** AI-03. **Depends on:** T13.1.

  **Business gates:** none.

  **Deliverables:** Independent final-text extraction, union with writer map and deterministic critical spans; validate the readiness evidence binding and final claims, and discard generation if relevant evidence changed while writing. Planned locations: `src/aam/drafting/; src/aam/validation/; tests/drafting/`.

  **Positive acceptance:** Applicable 30-day claim is supported by its actual source.

  **Negative acceptance:** An unmapped 999-day claim, unsupported commitment paraphrase, failed extraction, unknown source or copied illustrative fact blocks automation even when the writer map appears valid; example IDs cannot qualify as current evidence.

  **Closes:** AI-03-P, AI-03-N, AI-03-N2. **Contributes:** AI-03-P2, AI-03-N3.

  **Entry point and layer:** final-text claim extraction/coverage and critical-span validator (agents).

  **Verification command (planned):** `npm run test -- tests/agents/test_claim_validation.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T13.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.14 T14.3 Review validated model drafts and controlled factual warnings

  **Milestone:** M3. **Requirements:** AI-03, AU-02. **Depends on:** T14.2, T13.2.

  **Business gates:** D-13.

  **Deliverables:** New report on edit, visible factual warnings and policy-controlled acknowledgment bound to exact content; security and freshness controls cannot be overridden. Planned locations: `web/review/; src/aam/api/approvals/; tests/ui/; tests/e2e/edited-review.spec.ts`.

  **Positive acceptance:** An authorized warning acknowledgment under a synthetic policy permits a reviewed unchanged edit without looping back indefinitely.

  **Negative acceptance:** Absent override policy, changed content or forbidden recipient prevents approval/dispatch; no generic human-approval bypass.

  **Closes:** AI-03-P2, AI-03-N3. **Contributes:** AU-02-P, AU-02-N, AU-02-P2, AU-02-N2, AI-03-P, AI-03-N, AI-03-N2.

  **Entry point and layer:** edited-draft validation and warning-acknowledgment approval UI (ui).

  **Verification command (planned):** `npm run test -- tests/ui/test_edited_review.test.ts && npm run test:e2e -- tests/e2e/edited-review.spec.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T14.3/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.15 T21.2 Implement candidate evaluation and honest reports

  **Milestone:** M3. **Requirements:** QA-01. **Depends on:** T21.1, T13.2.

  **Business gates:** D-10.

  **Deliverables:** Scripted versus live model reports with numerators/denominators, per-slice sample counts, confidence method and unset-gate handling; compare current/candidate bundles on the same eligible holdout, recording example/selector versions and latency/token cost; final runner fails on incomplete output. Add workflow/predicate/connection versions and false-readiness, blocked-writer invocation and unsafe-tool-selection metrics; an unknown source cannot become an empty successful sample. Planned locations: `evals/; tests/evaluation/`.

  **Positive acceptance:** Reports identify exact model/bundle/corpus, selected-example configuration and comparable per-slice outcomes; no-example baseline and prior bundle can be reproduced without altering the holdout.

  **Negative acceptance:** Partial logs, skipped live execution, crashed model, unreviewed examples or tuning/holdout leakage cannot be summarized as a successful accuracy run; no automatic promotion from favorable aggregate scores.

  **Closes:** QA-01-P, QA-01-N. **Contributes:** none.

  **Entry point and layer:** candidate evaluation runner and actual final readiness report (evaluation).

  **Verification command (planned):** `npm run test -- tests/evaluation/test_candidate_report.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T21.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.16 T11.3 Enforce output readiness through generation and resume

  **Milestone:** M3. **Requirements:** AI-05. **Depends on:** T13.2, T06.3, T17.1, T14.1, T14.3, T15.2.

  **Business gates:** D-12, D-13, D-16.

  **Deliverables:** End-to-end guard integration across planner, children, writer, templates, human review and resume; PASS evidence bindings, typed gap/conflict decisions and durable wake conditions. Recheck relevant dependencies before output exposure and delivery. Planned locations: `src/aam/readiness/; src/aam/agents/; tests/integration/`.

  **Positive acceptance:** Complete evidence invokes the writer once with its exact snapshot; missing-data case survives restart, receives valid evidence and passes on a new attempt without duplicate commands.

  **Negative acceptance:** Missing country, unknown inventory, truncated extraction, conflicting source, forged PASS, stale evidence, direct child-writer call or human bypass creates no substantive draft; assert writer-call count zero before PASS and reject stale results after generation.

  **Closes:** AI-05-P, AI-05-N, AI-05-P2, AI-05-N2. **Contributes:** none.

  **Entry point and layer:** actual work handler, output request, writer boundary and approval/delivery precondition checks (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_output_readiness.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T11.3/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 3.17 T17.4 Author and simulate dynamic SOPs in the portal

  **Milestone:** M3. **Requirements:** CFG-04. **Depends on:** T17.3, T11.3.

  **Business gates:** D-12, D-13, D-16, D-18.

  **Deliverables:** Natural-language/form authoring, readable definition/predicate preview, authorized connection picker, positive/missing-input simulations and reviewed publication. Existing-case migration remains explicit; no code deployment for allowed SOP composition. Planned locations: `web/workflows/; src/aam/configuration/; tests/integration/; tests/e2e/sop-authoring.spec.ts`.

  **Positive acceptance:** AM publishes a renewal SOP using existing tools; distinct cases take permitted collection paths and each output observes its own predicate and approval rules.

  **Negative acceptance:** Unregistered connection, unknown field/operator, empty predicate contract, privilege escalation or prompt-injected publication is rejected; a new definition cannot silently change an active case.

  **Closes:** CFG-04-P, CFG-04-N, CFG-04-P2, CFG-04-N2. **Contributes:** none.

  **Entry point and layer:** portal authoring, simulation, publication and version-pinned runtime (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_sop_authoring.test.ts && npm run test:e2e -- tests/e2e/sop-authoring.spec.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T17.4/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

## 4. M4 Production qualification

- [ ] 4.1 T06.2 Implement approved idempotent write-back

  **Milestone:** M4. **Requirements:** IN-03. **Depends on:** T06.1, T15.2.

  **Business gates:** D-04.

  **Deliverables:** Feature-gated mapped CRM writes and separate command reconciliation. Read-only journey delivery does not depend on enabling write-back. Planned locations: `src/aam/integrations/crm/; tests/contracts/crm/`.

  **Positive acceptance:** Repeated accepted activity command has one business effect.

  **Negative acceptance:** Unauthorized field changes and ambiguous CRM responses remain rejected/uncertain, not completed.

  **Closes:** IN-03-N. **Contributes:** IN-03-P.

  **Entry point and layer:** CRM write-back adapter and command reconciliation (provider).

  **Verification command (planned):** `npm run test -- tests/provider/test_crm_writeback.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T06.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 4.2 T17.2 Implement controlled migration and extension example

  **Milestone:** M4. **Requirements:** CFG-02. **Depends on:** T17.3.

  **Business gates:** none.

  **Deliverables:** Migration impact/authorization and synthetic health-talk extension compatibility example; preserves type and parameter pins and records effects on approvals/timers. Planned locations: `resources/workflows/; src/aam/configuration/; tests/configuration/`.

  **Positive acceptance:** An explicitly authorized migration records changed state/approval impact.

  **Negative acceptance:** Silent active-case upgrade is rejected; extension does not change renewal behavior.

  **Closes:** CFG-02-P, CFG-02-N. **Contributes:** none.

  **Entry point and layer:** version migration API and synthetic extension interpreter (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_workflow_migration.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T17.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 4.3 T22.2 Implement authorized repair and replay

  **Milestone:** M4. **Requirements:** OP-02. **Depends on:** T22.1, T15.2, T16.2.

  **Business gates:** none.

  **Deliverables:** Operator commands with reason, scope, original IDs and audit. Planned locations: `src/aam/operations/; docs/runbooks/; tests/recovery/`.

  **Positive acceptance:** Repair resumes the same work without duplicate external actions.

  **Negative acceptance:** Unauthorized replay or stale command is rejected even through manual/fallback paths.

  **Closes:** OP-02-P, OP-02-N. **Contributes:** none.

  **Entry point and layer:** operator repair/replay API through actual worker execution (integration).

  **Verification command (planned):** `npm run test -- tests/integration/test_operator_recovery.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T22.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 4.4 T23.1 Harden production deployment and AKS operating model

  **Milestone:** M4. **Requirements:** OP-03. **Depends on:** T01.3, T22.1.

  **Business gates:** D-05, D-08, D-09, D-15.

  **Deliverables:** Production IaC and separate identities; effective grants, private data endpoints, polling egress, portal ingress, HA/backup, image scan, patch/upgrade ownership and qualified backlog autoscaling. No assumed Graph public webhook. Planned locations: `infra/; deploy/; tests/deployment/`.

  **Positive acceptance:** Fresh test environment provisions from declared parameters without self-hosted backend services.

  **Negative acceptance:** Missing region/identity configuration or accidental production connection fails deployment validation.

  **Closes:** none. **Contributes:** OP-03-P, OP-03-N, OP-03-N2.

  **Entry point and layer:** production IaC plan/deployment, grants and operating-policy checks (platform).

  **Verification command (planned):** `npm run test -- tests/platform/test_production_environment.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T23.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 4.5 T24.1 Enforce residency retention and protected telemetry

  **Milestone:** M4. **Requirements:** OP-01, OP-03. **Depends on:** T02.1, T05.1, T12.1, T23.1.

  **Business gates:** D-08, D-16.

  **Deliverables:** Approved policy covering every store/index/model/log/backup and scoped access. Planned locations: `docs/security/; tests/security/; infra/policies/`.

  **Positive acceptance:** Authorized retention/restore lifecycle handles all derived records consistently.

  **Negative acceptance:** Disallowed region, cross-account source access or secrets in logs blocks release.

  **Closes:** none. **Contributes:** OP-01-P, OP-01-N, OP-03-P, OP-03-N, OP-03-N2.

  **Entry point and layer:** data-policy admission/export/delete/retention across all stores (security).

  **Verification command (planned):** `npm run test -- tests/security/test_data_lifecycle.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T24.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 4.6 T23.2 Qualify deploy upgrade rollback and restore

  **Milestone:** M4. **Requirements:** OP-03. **Depends on:** T23.1, T24.1, T22.2, T17.2.

  **Business gates:** D-09, D-15.

  **Deliverables:** Cold deployment, node drain/upgrade, compatible rollback and ordered fenced restore runbook: DB, Blob, Search, waits/timers, mailbox/provider reconciliation, then dispatch reopen. Planned locations: `infra/; deploy/; tests/deployment/`.

  **Positive acceptance:** Recovered case state and bounded technical jobs meet approved objectives without replaying confirmed provider effects.

  **Negative acceptance:** Old-epoch workers, missing package/blob, sends accepted after backup or unresolved provider history prevent affected dispatch reopening.

  **Closes:** OP-03-P, OP-03-N, OP-03-N2. **Contributes:** none.

  **Entry point and layer:** fenced restore runbook, upgrade/drain and dispatch-reopen gates (platform).

  **Verification command (planned):** `npm run test -- tests/platform/test_restore.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T23.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 4.7 T25.1 Exercise actual acceptance paths under faults

  **Milestone:** M4. **Requirements:** QA-01, WF-01, WF-02, AU-03, CS-02, OP-01. **Depends on:** T18.3, T19.2, T20.2, T21.2, T22.2, T23.2, T10.2, T14.3, T06.2, T17.4.

  **Business gates:** none.

  **Deliverables:** Three composed journeys and all-access-path authorization tests; mutation guards for context, unmapped claims, approval dependencies, scope, event replay and closure. Retain actual final statuses and attributable provider evidence. Planned locations: `tests/e2e/; artifacts/release/; docs/reviews/`.

  **Positive acceptance:** All priority workflows complete with attributed final business state and source evidence.

  **Negative acceptance:** Duplicate/out-of-order events, pod termination and conflicting evidence produce correct final statuses without uncontrolled sends.

  **Closes:** OP-01-P, OP-01-N. **Contributes:** AU-03-P, AU-03-N, AU-03-N2, CS-02-P, CS-02-N, QA-01-P, QA-01-N, WF-01-P, WF-01-N, WF-02-P, WF-02-N.

  **Entry point and layer:** composed API-to-provider journeys and all-boundary security suite (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_acceptance_faults.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T25.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 4.8 T25.2 Complete independent correctness and delivery review

  **Milestone:** M4. **Requirements:** QA-01, WF-01, WF-02, AU-03, CS-02. **Depends on:** T25.1, T01.2.

  **Business gates:** none.

  **Deliverables:** Reviewed revision, findings, resolutions, CI runs and requirement evidence matrix. Planned locations: `tests/e2e/; artifacts/release/; docs/reviews/`.

  **Positive acceptance:** Qualified artifacts and all required tests are tied to the exact reviewed revision.

  **Negative acceptance:** Pending review, missing CI execution or unresolved critical findings cannot produce a ready-for-production declaration.

  **Closes:** none. **Contributes:** AU-03-P, AU-03-N, AU-03-N2, CS-02-P, CS-02-N, QA-01-P, QA-01-N, WF-01-P, WF-01-N, WF-02-P, WF-02-N.

  **Entry point and layer:** independent correctness/delivery review of qualified revision (review).

  **Verification command (planned):** `Independent reviewer executes the documented correctness and delivery protocol against the exact qualified commit; retain signed/attributed findings and closure in docs/reviews/. Silence is NOT RUN.`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T25.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

## 5. M5 Scoped automation

- [ ] 5.1 T26.1 Run shadow and approve category thresholds

  **Milestone:** M5. **Requirements:** QA-02. **Depends on:** T25.2.

  **Business gates:** D-10, D-13.

  **Deliverables:** Business-approved category gate records with denominator, minimum n, representative slices, confidence-bound method and accepted thresholds; shadow outcomes separated from synthetic results. Planned locations: `resources/policies/; docs/runbooks/automation/; tests/rollout/`.

  **Positive acceptance:** A qualified low-risk category has explicit measured acceptance and owner signoff.

  **Negative acceptance:** Small/unrepresentative samples, unset threshold or disqualifying outcome cannot qualify the category even with zero observed failures.

  **Closes:** none. **Contributes:** QA-02-P, QA-02-N.

  **Entry point and layer:** category gate evaluator on shadow evidence and approved metrics (evaluation).

  **Verification command (planned):** `npm run test -- tests/evaluation/test_shadow_gates.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T26.1/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.

- [ ] 5.2 T26.2 Exercise canary kill switch and rollback

  **Milestone:** M5. **Requirements:** QA-02. **Depends on:** T26.1.

  **Business gates:** D-13.

  **Deliverables:** Scoped automatic policy, monitoring, disablement and rollback evidence. Planned locations: `resources/policies/; docs/runbooks/automation/; tests/rollout/`.

  **Positive acceptance:** Only approved cohort sends automatically; disabling returns it to review-assisted processing.

  **Negative acceptance:** A serious violation disables affected automation without losing case history or retrying completed sends.

  **Closes:** QA-02-P, QA-02-N. **Contributes:** none.

  **Entry point and layer:** canary activation/kill-switch and rollback through policy API (journeys).

  **Verification command (planned):** `npm run test -- tests/journeys/test_automation_canary.test.ts`

  **Evidence:** Retain revision, environment, final assertions/status and diagnostics under `artifacts/<revision>/T26.2/`; provider/live evidence must be distinguished from fixtures. Current status: **NOT RUN**.
