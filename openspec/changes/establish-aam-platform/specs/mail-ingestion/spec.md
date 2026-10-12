## Purpose

Define the observable mail ingestion behavior of AAM, including failure handling and human oversight for regional client coordination.

## ADDED Requirements

### Requirement: IN-01 Recoverable mailbox ingestion

The system SHALL durably record each in-scope message with a stable mailbox-scoped identity and original-content reference before advancing its ingestion cursor. Repeated polling, folder moves within the supported mailbox boundary and duplicate notifications SHALL not duplicate messages or processing requests. Gaps SHALL be reconciled and failures and observation lag SHALL remain visible. Folder and mailbox scope SHALL follow an approved monitoring policy; an unconfigured scope SHALL not enable production ingestion.

Source stories: US-02.

#### Scenario: IN-01-P Positive acceptance

- **GIVEN** an authorized mailbox has two new messages
- **WHEN** a polling gap is reconciled
- **THEN** both messages are imported once and the reconciliation cursor advances only after durable recording

#### Scenario: IN-01-N Negative acceptance

- **GIVEN** the same delta result is returned twice and storage fails during the first attempt
- **WHEN** ingestion retries
- **THEN** no message is lost or duplicated and the failed attempt is diagnosable

### Requirement: IN-02 Safe attachment evidence

The system SHALL preserve attachment identity, content hash, extraction provenance, processing status and data-use classification. Unsupported, unsafe, encrypted or incompletely extracted material SHALL be quarantined or flagged rather than treated as fully understood evidence. Retention, model and search use SHALL follow approved data policy; unknown classification SHALL block model/index use until resolved.

Source stories: US-02.

#### Scenario: IN-02-P Positive acceptance

- **GIVEN** a supported claims deck is attached
- **WHEN** inspection and extraction succeed
- **THEN** its original and extracted content remain linked to the message with source locations

#### Scenario: IN-02-N Negative acceptance

- **GIVEN** a required pricing file is encrypted or extraction fails
- **WHEN** the agent requests its contents
- **THEN** the system reports unavailable evidence and does not infer that the submission is complete

### Requirement: IN-03 Authoritative CRM integration

The system SHALL retrieve account, stakeholder and renewal facts from the configured authoritative CRM with provenance and freshness. Approved write-backs SHALL be idempotent and limited to agreed fields. Missing or stale facts SHALL be explicit and SHALL block actions that depend on their accuracy.

Source stories: US-01, US-02.

#### Scenario: IN-03-P Positive acceptance

- **GIVEN** CRM contains a renewed policy date and authorized account owner
- **WHEN** synchronization completes
- **THEN** AAM uses the new facts and records source version and retrieval time

#### Scenario: IN-03-N Negative acceptance

- **GIVEN** CRM is unavailable or rejects a write-back
- **WHEN** a dependent action runs
- **THEN** AAM retains a visible retry or review state without inventing facts or reporting the write-back as completed

### Requirement: IN-04 Outbound correspondence observation

The system SHALL observe Sent Items in approved monitored mailboxes and attribute correspondence to human or AAM origin, retaining uncertainty when attribution or request linkage is unresolved. An observed human reply linked to a request SHALL supersede pending proposals and invalidate approvals for that request before further AAM dispatch. Ambiguous possible replies SHALL hold affected dispatch for resolution. Observation lag SHALL be exposed and actions dependent on stale observation SHALL be held under the approved freshness limit. This is not a guarantee that simultaneous external sends can be prevented.

Source stories: US-02, US-07, US-08.

#### Scenario: IN-04-P Positive acceptance

- **GIVEN** a human replies through Outlook to a request with a pending approved AAM draft
- **WHEN** the reply is ingested before AAM dispatch
- **THEN** the draft is superseded and no AAM send is issued

#### Scenario: IN-04-N Negative acceptance

- **GIVEN** a human sent item may answer two open requests or Sent Items synchronization is stale
- **WHEN** a potentially affected command reaches dispatch
- **THEN** the command is held with a correlation or observation-freshness diagnostic, not silently sent
