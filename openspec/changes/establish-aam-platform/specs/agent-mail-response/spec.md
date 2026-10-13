## Purpose

Define the observable agent mail response behavior of AAM, including failure handling and human oversight for regional client coordination.

## ADDED Requirements

### Requirement: AI-01 Typed bounded specialist processing

The system SHALL extract current-message intent, substantive questions, entities and proposed obligations into validated structured outputs. A guarded runtime SHALL allow dynamic planning within the pinned SOP while enforcing permitted tool capabilities, mandatory readiness/validation boundaries and aggregate model/tool budgets. Missing, malformed, timed-out or interrupted output SHALL not become success or an executable proposal. Reviewed demonstrations SHALL come from the pinned, validated bundle and be selected deterministically within stage budgets. Analysis selection SHALL support unknown workflow without depending on the intent being predicted. Stage records SHALL retain selected example IDs and bundle/selection versions; a valid zero-match selection SHALL be distinguishable from an unavailable or invalid bundle.

Source stories: US-02, US-07.

#### Scenario: AI-01-P Positive acceptance

- **GIVEN** a complete email requests two country updates
- **WHEN** analysis and downstream stages complete
- **THEN** both requests are represented and validated stage outputs are retained; known-workflow and unknown-workflow variants retain bounded, reproducible example selections, with a valid zero-example variant supported

#### Scenario: AI-01-N Negative acceptance

- **GIVEN** a stage returns missing fields, partial JSON or crashes after printing plausible success text, or requires an unavailable/malformed example bundle
- **WHEN** the run finishes
- **THEN** the structured result is failure or review-required and no delivery command is created

### Requirement: AI-02 Scoped evidence and coverage

The system SHALL retrieve only authorized, applicable and approved knowledge and facts, retain provenance, and report sufficient, partial, missing or conflicting coverage for the current questions. Retrieval limits SHALL be explicit; reaching a limit SHALL not imply full coverage. Model-supplied scope SHALL not override trusted access filters.

Source stories: US-02, US-07.

#### Scenario: AI-02-P Positive acceptance

- **GIVEN** two questions require different applicable SOP passages
- **WHEN** retrieval completes within its budget
- **THEN** coverage and evidence are recorded for each question

#### Scenario: AI-02-N Negative acceptance

- **GIVEN** sources conflict, are expired, cross client boundaries or support only one question
- **WHEN** coverage is evaluated
- **THEN** the result exposes the limitation and cannot authorize a complete substantive answer

### Requirement: AI-03 Factual draft validation

The system SHALL independently extract consequential claims from the final recipient-visible text and compare their coverage with writer-supplied mappings. Deterministic critical-value and commitment-span checks SHALL supplement semantic checks. Missing mappings, unknown evidence IDs, unsupported or refuted consequential claims SHALL block automatic sending. Human edits SHALL receive a new validation report tied to their content hash. Any human override of factual warnings SHALL require an explicitly configured authority and recorded acknowledgment; missing override policy SHALL hold the action. No override SHALL bypass mandatory output readiness, account scope, recipient authorization, freshness, or prohibited-action controls. Model review alone SHALL not grant authority.

Source stories: US-02, US-07.

#### Scenario: AI-03-P Positive acceptance

- **GIVEN** a source says 30 days and the draft accurately states the applicable rule
- **WHEN** validation runs
- **THEN** supporting source/version and verification outcome are recorded

#### Scenario: AI-03-N Negative acceptance

- **GIVEN** the same source is cited for 999 days or the draft claims an unperformed escalation
- **WHEN** validation runs
- **THEN** automatic sending is blocked with a specific unsupported/refuted claim diagnostic

#### Scenario: AI-03-N2 Unmapped consequential text

- **GIVEN** the final text promises 999 days but the writer omits that claim from its map
- **WHEN** independent validation runs
- **THEN** the unmapped span is identified and automatic sending is blocked

#### Scenario: AI-03-P2 Human edit with authorized warning handling

- **GIVEN** an authorized reviewer supplies an externally learned fact and an approved policy permits acknowledging its factual warning
- **WHEN** the edited text is validated and approved
- **THEN** the fact is recorded under its permitted attestation rule, readiness is reevaluated, and the warning and acknowledgment bind to the exact content; the action stays reviewed and all non-overridable controls still apply

#### Scenario: AI-03-N3 Human approval is not a universal bypass

- **GIVEN** a human edit has no applicable warning-override policy or uses an unauthorized recipient
- **WHEN** approval is requested
- **THEN** the system holds or rejects the action without creating an approval loop or bypassing controls

### Requirement: AI-04 Appropriate conversational responses

The system SHALL draft in the current message language, preserve conversational continuity and request only necessary missing information. Customer-facing text SHALL exclude internal evidence IDs, prompts and diagnostics. Permitted acknowledgment or clarification templates SHALL be distinguishable from a substantive answer and SHALL make no unsupported commitment. Writing demonstrations SHALL be selected by workflow, intent and language with bounded deterministic fallback or zero matches. Illustrative replies SHALL not supply current facts, satisfy evidence coverage, establish completed work or change action authority; missing examples SHALL not relax existing controls.

Source stories: US-02, US-07.

#### Scenario: AI-04-P Positive acceptance

- **GIVEN** a customer provides information requested in the preceding message
- **WHEN** a response is drafted
- **THEN** it acknowledges the new information and asks only for remaining required details, using relevant reviewed demonstrations when available and retaining the same controls when none match

#### Scenario: AI-04-N Negative acceptance

- **GIVEN** a prior message contains secrets, the knowledge result is insufficient, or a selected illustrative reply contains a date, amount or completed-action statement unsupported for the current case
- **WHEN** a clarification is prepared
- **THEN** it neither repeats secrets nor disguises an unsupported policy answer as an acknowledgment, and it does not copy illustrative facts or claim completion from an example

### Requirement: AI-05 Mandatory output readiness

Every workflow SHALL declare a validated entry predicate and a readiness predicate for each named substantive output, separate from action approval and case completion. Application code SHALL evaluate required presence, scope, authority, freshness, collection completeness and consistency from registered evidence. Only PASS SHALL allow the corresponding writer invocation and release of its draft. Missing, unknown, partial or conflicting mandatory information SHALL hold that output without guessing or silently narrowing its scope. PASS SHALL bind the output contract and exact evidence/workflow/predicate versions and SHALL be reevaluated after relevant changes or expiry. Models, subagents, retries, templates and human approvals SHALL not bypass this boundary. Permitted requests for missing information SHALL use a separate output contract and SHALL not contain the blocked substantive answer.

Source stories: US-02, US-06, US-07.

#### Scenario: AI-05-P Complete input permits generation

- **GIVEN** a configured output has current authorized facts for every mandatory field and expected country
- **WHEN** application code evaluates its predicate
- **THEN** a version-bound PASS permits the writer to use exactly that evidence snapshot and the later validation/approval gates still apply

#### Scenario: AI-05-N Missing or unknown information

- **GIVEN** one required quote is missing, an inventory is unknown, extraction is incomplete or applicable sources conflict
- **WHEN** any path requests the substantive output
- **THEN** the writer is not invoked, no substantive draft is exposed, and a recorded gap/conflict determines collection, review or waiting

#### Scenario: AI-05-P2 Resume after resolving gaps

- **GIVEN** a held case receives the missing authorized evidence after a restart
- **WHEN** its recorded wake condition starts a new work attempt
- **THEN** readiness is reevaluated on current evidence and only a new PASS permits output; duplicate wakeups do not create duplicate commands

#### Scenario: AI-05-N2 Bypass or stale decision

- **GIVEN** an agent claims ready, a child invokes the writer directly, an approver attempts to waive a missing required input or evidence changes after PASS
- **WHEN** generation or delivery is attempted
- **THEN** the bypass or stale decision is rejected, affected output is withheld and current readiness must be established again
