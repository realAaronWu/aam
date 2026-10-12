## Purpose

Define the observable case tracking behavior of AAM, including failure handling and human oversight for regional client coordination.

Business policy dependencies are recorded in [the decision register](../../../../../docs/product/decisions.md). Scenarios with approved policies use explicit synthetic fixtures; they do not select production business rules.

## ADDED Requirements

### Requirement: CS-01 Case identity across conversations

The system SHALL identify cases independently of email threads and support multiple threads per case and multiple requests within one thread. Authorized split, link, merge and reassignment actions SHALL preserve history, permissions and the original message references.

Source stories: US-01, US-02.

#### Scenario: CS-01-P Positive acceptance

- **GIVEN** a renewal continues in a new email thread
- **WHEN** an authorized linkage is confirmed
- **THEN** both conversations appear in one case with their original identities

#### Scenario: CS-01-N Negative acceptance

- **GIVEN** an attempted merge spans accounts without matching authorization
- **WHEN** the merge is requested
- **THEN** the system rejects it without exposing or moving the other account records

### Requirement: CS-02 Evidence based obligations and closure

The system SHALL track obligation owner, due date, dependencies, status and accepted completion evidence. Evidence-source, confirmation and waiver rules for each obligation type SHALL be explicitly configured by the business owner (D-12); absent rules SHALL block activation or completion of dependent work. A promise, draft, acknowledgment, accepted send request or successful agent run SHALL not by itself satisfy an obligation or close a case. Waivers SHALL require configured authority and recorded reasons.

Source stories: US-01, US-03, US-04.

#### Scenario: CS-02-P Positive acceptance

- **GIVEN** all mandatory obligations have accepted evidence or authorized waivers
- **WHEN** case closure is requested
- **THEN** the case closes with the supporting decisions recorded

#### Scenario: CS-02-N Negative acceptance

- **GIVEN** an local team says it will submit tomorrow or only a reminder has been sent
- **WHEN** the case is evaluated
- **THEN** the obligation and case remain outstanding

### Requirement: CS-03 Current case overview and history

The system SHALL show structured case status, outstanding tasks, owners, deadlines, approvals and evidence-linked history. AI summaries SHALL identify their source case version and refresh time. Committed facts SHALL remain visible independently of summary generation. Concurrent events SHALL have stable causal ordering.

Source stories: US-01, US-02, US-05.

#### Scenario: CS-03-P Positive acceptance

- **GIVEN** a response satisfies a task while narrative generation is delayed
- **WHEN** the Account Manager opens the case
- **THEN** the current structured state is shown and the older summary is labeled with its version/time

#### Scenario: CS-03-N Negative acceptance

- **GIVEN** a company reply and its subsequent customer follow-up share a timestamp
- **WHEN** history is displayed
- **THEN** the follow-up remains after its causal predecessor rather than being reordered by message type

### Requirement: CS-04 Proposed obligations remain distinct

The system SHALL persist model-proposed tasks, owners and due dates separately from authoritative obligations. Promotion SHALL require a recorded decision under a configured business rule and actor authority; missing policy SHALL leave the proposal pending. Promotion SHALL be idempotent and audit the original proposal, selected owner/date and policy version. The business owner defines the promotion rules (D-16).

Source stories: US-01, US-02.

#### Scenario: CS-04-P Positive acceptance

- **GIVEN** an approved synthetic promotion policy and authorized decision accept one proposal
- **WHEN** the decision is submitted twice
- **THEN** one obligation is created with the decision and source proposal retained

#### Scenario: CS-04-N Negative acceptance

- **GIVEN** a model proposes a due date but no promotion policy is approved
- **WHEN** processing completes
- **THEN** the proposal remains pending and creates no authoritative deadline or reminder
