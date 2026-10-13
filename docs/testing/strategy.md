# AAM testing and acceptance strategy

Status: implementation tests are planned and NOT RUN. Current repository checks validate planning structure only. Requirements, scenarios and exactly one closing task per scenario are indexed in [traceability.json](../traceability.json).

## Evidence model and completion gate

For each requirement and task, retain PASS / FAIL / NOT RUN, source revision, dependency-lock digest, test/corpus version, model/deployment/bundle/workflow versions where applicable, environment, command, timestamp, final business/technical status and diagnostic/artifact references. A failed prerequisite is not an empty successful report. Cancellation/interrupt is distinct from application failure. Record immutable artifact references; do not place customer data or secrets into committed reports.

A task is complete only when its deliverables exist, positive and negative acceptance cases exercise the actual relevant entry point, applicable type/lint/contract checks pass, and evidence is linked. Each task declares Closes and Contributes. A closing task owns the complete scenario proof; contributing tasks claim only their own local acceptance. Every scenario must have exactly one closing owner in Markdown and traceability.json. A requirement becomes PASS only after every applicable scenario/layer is satisfied, not when the first contributing task completes. Correctness and delivery review are required before production readiness or PR readiness; record reviewed revision, findings and closure. No response from a reviewer is NOT RUN, not approval.

## Test layers

| Layer | What it proves | Key examples | Proposed execution |
| --- | --- | --- | --- |
| Specification checks | Artifact structure, links, requirement/task coverage, no lost stories | Missing scenario/task, invalid dependency, duplicated ID | `npm run spec:validate` now; additional traceability automation in T01 |
| Domain unit tests | Deterministic business rules | Obligation completion, policy, calendars, stale versions | `npm run test -- tests/unit` after T01 |
| Contract tests | Valid/invalid API, event, tool and provider envelopes | Wrong schema/types, absent evidence, malformed output, unexpected headers | `npm run test -- tests/contracts` after T01 |
| PostgreSQL integration | Real transactions, constraints, outbox/inbox and concurrent state updates | Crash around commit/publication, duplicate events, cancellation race | Required integration suite; SQLite is not a substitute |
| Agent component tests | Deep Agents tool boundaries, typed handoffs, readiness, scopes, budgets and failure handling | Original-email substitution, unknown evidence IDs, partial outputs | Scripted model/tool responses; does not establish model accuracy |
| Live-model evaluation | Measured behavior on a labeled corpus | Coverage, contradiction, broker ambiguity, language, injection | Explicit controlled evaluation job; pin model and corpus |
| Workflow integration | Durable runtime behavior | DB wait/timer reconstruction, bounded job retry, stale epoch fencing, timer cancellation and type compatibility | Real PostgreSQL concurrency/crash tests plus Azure deployment qualification |
| Provider sandbox tests | Actual connector contracts | Graph per-folder delta and Sent Items, scoped draft/send, lost-response reconciliation, optional CRM write-back | Approved isolated identities and destinations only |
| Full journey tests | Composed final behavior | Inbound/CRM trigger through case, proposal, approval, send and obligation outcome | Three priority journeys with observable external effects |
| Operational qualification | Deployment, protection, restore and incident handling | Cold deploy, replica failover, restore pending commands, retention | Isolated Azure environment; attributable runbooks/results |

The commands other than `npm run spec:validate` describe future test layout, not currently executable application suites. T01 must turn these into real discovered CI jobs and retain logs on failure. Choose the CI host under D-15; no provider is assumed. T01.3 supplies the early test environment and test identities, while T23 hardens production. Task-specific entry points, layers and commands are recorded in tasks.md; they are not all full-journey tests.

## Priority acceptance paths

1. **Renewal:** inject CRM eligibility, create multiple country obligations, collect pricing, receive revised terms, approve release, simulate negotiation and record final confirmation. Assert accepted versions, outstanding-country handling, no premature closure and stopped obsolete reminders. Interrupt a worker during a wait and repeat the trigger/events.
2. **Quarterly local reporting:** schedule a quarter, create country obligations, process a promise, then a wrong-quarter deck, then accepted delivery evidence. Only the final valid evidence completes the correct obligation. Verify the recipient/country/period and stop follow-up for completed work.
3. **Program change:** receive a notice accepted under an explicit synthetic or business-approved authority policy, resolve affected entities, prepare and approve notices, then track next actions. Reject unverified senders and unsupported coverage commitments. A successful send alone leaves incomplete actions open.
4. **Multi-client broker:** one sender represents A and B, and a forwarded thread changes the referenced policy. Assert authorized resolution, scoped retrieval, draft recipients and case links. Test unresolved and conflicting identities, legitimate multi-client mail with an unset handling policy, and one local team serving two renewals. Hold missing-policy paths rather than inventing response rules.
5. **Human correspondence:** observe a human Outlook reply before approved AAM dispatch, assert proposal supersession and no send. Test ambiguous linkage and stale folder watermarks. Separately exercise a simultaneous human/provider send after the observation barrier; record the race and subsequent reconciliation rather than assert impossible prevention.
6. **Business-policy absence:** missing promotion, evidence, recipient, consolidation or authority rules hold their specific paths with diagnostics. Synthetic fixtures are labeled and never accepted as business decisions.

## Challenge the acceptance boundary

| Adverse input or mutation | Required observed outcome |
| --- | --- |
| Change original email/account in a delegated input | Rejection before privileged retrieval or proposal authorization |
| Omit a 999-day final-text claim from the writer map | Independent extraction flags the consequential span and blocks automation |
| Approve an edit with missing override authority or unauthorized recipient | Hold/reject without a blanket human-approval bypass |
| Cite a real 30-day source for a 999-day answer | Refuted/unsupported claim; no automatic command |
| Remove evidence, current-message content or required structured fields | Explicit invalid/incomplete result; never a successful empty answer |
| Crash with expected success/error words in stdout | Structured process failure governs; matching text cannot certify success |
| Timeout after partial model/tool output | Timeout with retained safe diagnostics; partial output not accepted |
| Change recipient/body/attachment after approval | Approval invalidated; dispatch blocked |
| Inject another client's identifier into retrieval filters | No cross-client results or disclosure |
| Deliver duplicate/out-of-order callbacks or cancel during dispatch preparation | Idempotent outcome and current-state enforcement |
| Provider accepts send but response is lost | Uncertain outcome and reconciliation; no blind resend |
| Change an unrelated country event versus a referenced term version | Preserve approval for the former; invalidate it for the latter |
| Restore DB behind worker/provider side effects | Fence old execution; reconcile post-backup sends; unknown state keeps dispatch disabled |
| Exceed Graph/model quota during node drain | Bounded retry honors provider delay; work persists, shutdown stops claims and recovery retains uncertainty |
| Bypass test worker recipient allowlist in an isolated negative probe | Independent provider transport restriction blocks outside destinations |
| Two causally ordered events share a timestamp | Stable causal ordering in case history |
| Change expected labels or corpus membership without provenance update | Evaluation invalid, not silently comparable to prior results |
| Remove a guard in a disposable mutation | Its dedicated regression test fails through the real acceptance path |

Mutation checks target context integrity, evidence checks, approval binding, idempotent commands and closure rules. Never commit the mutations or relax expectations to make them pass. After any false acceptance, inspect normal, retry, manual-repair and fallback paths for the same failure class.

## Accuracy evaluation

Start T21.1 in M1 with a synthetic seed dataset. Obtain data-owner approval for de-identified real samples before retention/model use, and track which slices remain unrepresented. Synthetic examples challenge guards but do not establish production prevalence or accuracy. Maintain an approved synthetic/de-identified dataset with frozen holdout and documented labels. Include all three journeys, multiple clients per broker, country and period ambiguity, multilingual correspondence, multi-question emails, partial documents, promises, superseded terms, source conflicts, out-of-office replies and prompt injection. Domain experts resolve disputed labels. Track corpus/label hashes and prevent tuning-set leakage into the holdout.

Report account-match precision and abstention coverage; extraction precision/recall by field; per-question coverage accuracy; unsupported consequential-claim rate; false obligation-completion rate; unauthorized-action rate; AM correction rate; latency and cost. Report numerators, denominators and uncertainty where sample size matters. Slice by workflow, client pattern, language and risk; an overall average cannot hide a failing slice.

A category gate records metric definition, denominator, minimum sample size, confidence-bound method, acceptable bound, required slices and disqualifying outcomes; D-10/D-13 supply business-approved values. Use an explicit binomial confidence method for binary error rates; zero observed errors is not zero risk. Rare categories with insufficient samples remain reviewed. Shadow observations on representative approved traffic supplement, rather than replace, adversarial challenge tests. Deterministic templates qualify policy, recipients, variables and cadence without an irrelevant model gate; model-generated content additionally needs representative live-model evidence. Automatic-send thresholds require a business-approved policy and sufficiently representative evidence for that category. Do not substitute a model's self-reported confidence or retrieval similarity for calibrated accuracy. Any observed cross-client disclosure or unauthorized commercial commitment blocks the affected rollout. Zero observed violations in a small dataset does not establish zero production risk.

### Reviewed example library checks

T21.1 splits source conversations/scenario families into tuning and held-out sets before example curation. Runtime demonstrations come only from reviewed tuning material; holdout files are excluded from the worker bundle. Reject ID/content-hash/source-group overlap and review paraphrased near-duplicates; hash equality alone does not prove independence. A correction promoted from evaluation to tuning retires that conversation family from the holdout and requires a newly versioned evaluation baseline. No raw customer mail or secrets enter Git.

T11.1 tests real bundle loading, content digest changes, deterministic selection, unknown-workflow balanced sampling, exact intent/language preference, multi-question allocation, zero matches and whole-example dropping under token limits. Malformed/missing bundles, duplicate IDs and invalid labels are failures, not zero-example success. T11.2 exercises selection through the actual guarded dynamic handler, asserting stage metadata and unmodified current email/context. T13.1/T13.2 challenge copied illustrative prices/dates and unperformed actions, including example IDs presented as evidence; final validation must block unsupported claims. These scripted tests verify controls, not model accuracy.

T21.2 evaluates current and candidate bundles on the same untouched holdout and optionally a no-example baseline, with fixed model/settings where supported. Report per-intent accuracy and abstention, unsupported claims, AM corrections, token cost and latency; retain exact bundle, selection and corpus versions. A beneficial average cannot waive a failing safety slice or an unset business gate. Content release requires review, structural/negative checks and the targeted regression subset; any claim of model improvement or wider automation additionally requires representative live-model evidence. All these implementation tests are currently NOT RUN.

## Dynamic SOP, connection and readiness acceptance

T17.1 establishes shared typed readiness evaluation for both deterministic templates and model-generated outputs. T06.3 qualifies D365/Dataverse, Outlook/Graph and approved knowledge adapters: allowed/denied account and field access, application versus delegated identity assumptions, pagination, token refresh, revocation, source outages, scoped caches and MCP schema drift. Use actual isolated provider calls for adapter qualification; mocks cannot prove tenant grants. Record only safe metadata in logs.

T11.1/T11.2 inspect the effective main and child tool inventories in the pinned Deep Agents version. Challenge default filesystem/shell tools, uncontrolled delegation, writable skills, provider write calls and prompt-injected scope changes. Restart must reconstruct a bounded read/planning attempt from DB state; no subagent todo/checkpoint can approve, send or complete a case. Aggregate budgets include children. Dynamic valid tool ordering is permitted; skipping mandatory application gates is not.

T11.3 exercises the actual work-handler-to-output boundary with instrumented writer calls. Complete inputs produce one matching PASS and bound invocation. Missing quote, unknown/empty expected-country inventory, unfinished pagination, stale cache after a failed refresh, contradictory evidence, truncated attachment and null fields produce zero substantive writer calls. A late source-version change invalidates already-generated output before exposure. Test direct child writer calls, forged PASS IDs, human approval of missing inputs, template paths, replay after expiry and restart/wakeup. Separately verify that an authorized missing-information template meets its own minimal predicate without containing the blocked answer. Targeted mutations bypassing predicate evaluation, treating unknown as true or dropping the source-version check must be detected through the real handler/dispatch path.

T17.4 verifies AM authoring from natural language and forms through preview, complete/missing-input simulation, authorized publication and pinned execution. Reject unknown fields/operators, empty contracts, newly discovered unregistered tools and unauthorized predicate relaxation. T17.4 publishes a changed SOP and proves active cases remain pinned or reject silent change. T17.2 owns actual migration execution and its approval/timer impact checks in M4. Track false-readiness rate, zero-call-before-PASS violations and unsafe-tool selections in T21.2 alongside response accuracy. Passing structural checks or model confidence cannot establish these gates.

All of these are planned checks, currently NOT RUN. No completeness predicate or model validator proves that unknown real-world facts cannot exist; acceptance establishes that the declared mandatory input and authority contracts were enforced against observable evidence. Unresolved scope/meaning remains a hold.

## Proposed service qualification targets

These are initial design targets for D-09 review, not source requirements or an SLA promise. Approve workload, observation window, region and provider assumptions before qualification.

| Measure | Proposed target and measurement |
| --- | --- |
| Structured case freshness | p95 within 5 seconds of committed case event under agreed load |
| Narrative freshness | p95 within 60 seconds of committed event when dependencies are healthy; always show stale version/time otherwise |
| Intake recovery | All controlled missed-poll test messages recovered once after per-folder delta reconciliation; report recovery latency separately |
| Restart safety | No lost accepted case transition or intentionally duplicated command in the fault suite |
| Business-store recovery | Proposed RPO at most 15 minutes and RTO at most 4 hours; validate database, Blob, scheduler and external-action reconciliation as one recovery procedure |
| Cost and capacity | Establish expected messages/day, attachment size, active cases, concurrent waits and burst load; report token/tool budgets and saturation behavior before sizing |

Do not claim zero cross-service data loss from one service's HA setting. Restore tests follow the design runbook: fence old identities/workers; restore DB into a new recovery epoch; verify Blob/package references; rebuild authorized Search; rebuild waits/timers/work; reconcile inbound and Sent Items plus commands accepted after the backup; only then qualify reopening. Missing provider history holds affected dispatch. Test AKS upgrade/node drain during model activities, queue saturation, outbox transfer acknowledgement loss, job lease expiry, attempt-history cleanup and unavailable package versions.

## CI and delivery gates

T01 establishes required PR jobs for specs, lint/types, unit/contract tests and cheap negative cases. Changes to persistence require real PostgreSQL integration; workflow changes require recovery/timer tests; changes to agent behavior require the pinned evaluation subset. Live-model and Azure qualification can run in separately authorized environments, but their absence remains NOT RUN. Keep untrusted PR runs separate from credentials and external action capabilities.

Verify job triggers, path filters, test discovery, matrices, dependency ordering, concurrency, source revision, artifact provenance and failure propagation. Always retain failed logs and summaries. A valid YAML file or successful local command does not prove CI ran. Production qualification uses clean checkout and declared dependencies, including a cold-cache provisioning run where relevant.

## Planning checks and checker challenge

The current revision is assessed for strict OpenSpec syntax, unique IDs, story/requirement parity, exact task dependency parity, DAG order, relative links, public file inventory and one closing task for every scenario. Challenge planning checks in disposable data with a missing closing owner, duplicate owner and dependency cycle. This is planning validation only; T01.2 must persist equivalent checks in the chosen CI system. Model/provider correctness is not established by these checks.

## Rollout

Offline fixtures → provider sandbox → review-assisted journeys → live-model shadow evaluation → approved low-risk canary → monitored expansion. Keep a policy kill switch and reviewed operation available throughout. Reverting automation does not discard case history or rewind external actions. Each stage has a recorded owner and evidence decision; elapsed time alone never advances a gate.

## TypeScript runtime and reuse qualification

T01.1 establishes the locked Node.js/TypeScript toolchain and `npm run test` (Vitest run), `npm run typecheck`, `npm run build` and `npm run test:e2e` (Playwright) scripts. Commands in tasks are planned after that foundation exists; the present planning package does not yet implement them. Fail on empty test discovery. Vitest covers contracts, application modules and provider integration harnesses; real browser flows use Playwright with isolated identities and controlled adapters. Live provider and model tests require their explicitly qualified environments.

T11.1 verifies the actual locked Deep Agents TypeScript runtime, including child and summarization calls sharing budgets, aborted calls, hidden default tools and malformed output. A fixture-only runtime cannot satisfy that gate. T12.1 rejects unknown evidence IDs and same-ID/different-content collisions. T11.2 verifies immutable server-built handoffs and dynamic valid collection order; T11.3 verifies the per-output writer boundary. Cross-language reference tests are porting inputs, not TypeScript compatibility evidence.

T03.2 and T16.1 run against real PostgreSQL with two worker processes: atomic outbox transfer, competing claims, lease renewal/loss, cancellation, lost commit acknowledgements, late results from fenced owners, bounded retry/quarantine, recovery epochs and retained business deduplication after attempt cleanup. T22.1 fails readiness and stops new admissions/claims on critical persistence faults. T23.2 exercises the same controls during AKS drain/restore; delivery uncertainty still uses provider reconciliation, never blind job retry. All new application checks are NOT RUN.

Browser acceptance ownership is explicit: T10.1 case overview/evidence, T10.2 summary freshness, T14.2 exact approvals, T14.3 edited review, T17.3 parameter publication and T17.4 SOP authoring each retain module/API tests and run their named `tests/e2e/*.spec.ts` Playwright flow. Their traceability records include `browser_test_file` and both commands; CI discovery must include both runners and retain failed browser traces. Module success alone does not close browser acceptance.
