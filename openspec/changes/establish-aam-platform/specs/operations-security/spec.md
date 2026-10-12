## Purpose

Define the observable operations security behavior of AAM, including failure handling and human oversight for regional client coordination.

## ADDED Requirements

### Requirement: OP-01 Account scoped authorization and audit

The system SHALL authenticate users and workloads and enforce role/account authorization at every API, retrieval, approval and execution boundary. It SHALL audit decisions and state changes with actor, case, versions, reasons and evidence references while protecting secrets and restricted content.

Source stories: US-02, US-08.

#### Scenario: OP-01-P Positive acceptance

- **GIVEN** an assigned Account Manager reviews a case
- **WHEN** authorized reads and approvals occur
- **THEN** each action is permitted within scope and attributable in the audit history

#### Scenario: OP-01-N Negative acceptance

- **GIVEN** another account user requests the case or a model supplies broader scope
- **WHEN** any access path is exercised
- **THEN** access is denied without disclosing restricted data, including through search or summaries

### Requirement: OP-02 Operator recovery and diagnostics

The system SHALL expose stalled work, ingestion lag, failed delivery, dead-letter events and uncertain outcomes to authorized operators. Repair, replay, pause, cancel and reassignment SHALL preserve deduplication, authorization and audit invariants. Technical failure SHALL remain distinct from missing knowledge.

Source stories: US-08.

#### Scenario: OP-02-P Positive acceptance

- **GIVEN** an operator repairs a transient connector outage
- **WHEN** a failed event is replayed
- **THEN** the same case resumes without duplicate actions and the repair is recorded

#### Scenario: OP-02-N Negative acceptance

- **GIVEN** a replay is unauthorized or a critical persistence dependency fails
- **WHEN** processing is attempted
- **THEN** unsafe work stops visibly and cannot be reported successful through a fallback path

### Requirement: OP-03 Managed deployment and data lifecycle

The system SHALL use managed cloud backend services and environment-isolated identities and data. Deployment SHALL support controlled upgrades, restore and recovery with declared recovery objectives. Retention, deletion, audit access and residency rules SHALL cover original messages, extracted data, search indexes, snapshots and backups.

Source stories: US-08.

#### Scenario: OP-03-P Positive acceptance

- **GIVEN** an approved release is deployed to an isolated environment and restore is exercised
- **WHEN** verification runs
- **THEN** the release and recovered case state are attributable to documented versions and objectives

#### Scenario: OP-03-N Negative acceptance

- **GIVEN** an environment points to a disallowed region or restore lacks pending-action reconciliation
- **WHEN** deployment or recovery is assessed
- **THEN** production activation is blocked until policy and recovery evidence are complete

#### Scenario: OP-03-N2 Database restore fences stale execution

- **GIVEN** the database is restored behind scheduler history and an external send may have happened after the restore point
- **WHEN** recovery begins
- **THEN** old execution is fenced, waits/timers are reconstructed, potentially affected sends are reconciled or held, and dispatch stays disabled until recovery is authorized

### Requirement: OP-04 Scoped provider access and environment guards

The system SHALL constrain provider read, draft-write and send identities to approved mailboxes, with effective permissions verified beyond application-side filtering. Non-production delivery SHALL enforce independent application and provider recipient restrictions. A broader identity grant or inability to prove the boundary SHALL block activation. The mailbox inventory and recipients remain business/platform inputs, not inferred from message content.

Source stories: US-07, US-08.

#### Scenario: OP-04-P Positive acceptance

- **GIVEN** test identities and destinations are explicitly allowed at application and provider layers
- **WHEN** a sandbox command is submitted
- **THEN** the expected identity submits within scope and both guard configurations are retained as evidence

#### Scenario: OP-04-N Negative acceptance

- **GIVEN** an identity targets an unscoped mailbox or a non-production recipient outside its allowlist
- **WHEN** provider access or dispatch is attempted
- **THEN** access is denied at the applicable independent boundary and no unrestricted sending is enabled
