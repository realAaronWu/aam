## Purpose

Define the observable quality evaluation behavior of AAM, including failure handling and human oversight for regional client coordination.

## ADDED Requirements

### Requirement: QA-01 Representative accuracy evaluation

The system SHALL evaluate account resolution, extraction, coverage, output readiness, tool authorization, unsupported claims, obligation completion and action policy using versioned representative positive and adverse examples. Reports SHALL identify corpus, bundle and model versions and separate fixture, live-model and end-to-end evidence. Missing evidence SHALL be NOT RUN or failure, never success. Runtime demonstrations SHALL be domain-reviewed synthetic or approved de-identified records, published through the versioned bundle release process. Approving a live reply SHALL not automatically publish an example. Held-out evaluation records and related source conversations SHALL remain separate from runtime demonstrations and tuning data; overlap or unreviewed content SHALL invalidate the affected release/evaluation claim.

Source stories: US-02, US-07, US-08.

#### Scenario: QA-01-P Positive acceptance

- **GIVEN** a candidate prompt/model is evaluated on the approved corpus
- **WHEN** the report is produced
- **THEN** per-slice metrics, failures and version provenance are available for comparison against the current bundle; example selection settings and a checked tuning/holdout split are recorded

#### Scenario: QA-01-N Negative acceptance

- **GIVEN** a fixture passes but live-model execution is absent or output is incomplete, or a candidate includes unreviewed examples or overlaps the held-out set
- **WHEN** release readiness is assessed
- **THEN** missing live-model accuracy is NOT RUN, and incomplete output, unreviewed examples or holdout overlap fails the affected gate; the candidate cannot claim that gate passed

### Requirement: QA-02 Controlled automation release

The system SHALL enable automatic actions only for business-approved categories with satisfied category-specific evaluation and operational gates. A gate SHALL name the metric, denominator, minimum sample size, confidence-bound method, acceptable bound, representative slices and disqualifying outcomes. Unset values or insufficient representative evidence SHALL prevent qualification. Deterministic templates need policy/recipient/cadence evidence without an irrelevant LLM accuracy dependency; model-generated content additionally needs model evaluation. Shadow and canary operation, immediate disablement and rollback SHALL preserve history. These mechanisms do not decide which categories may automate (D-13).

Source stories: US-07, US-08.

#### Scenario: QA-02-P Positive acceptance

- **GIVEN** a low-risk category meets approved thresholds and has an accountable owner
- **WHEN** its canary is authorized
- **THEN** only the configured cohort becomes automatic with monitored outcomes

#### Scenario: QA-02-N Negative acceptance

- **GIVEN** a cross-client disclosure or unsupported commitment occurs, or thresholds are unset
- **WHEN** activation or continued operation is evaluated
- **THEN** affected automation is disabled or blocked while reviewed case processing remains available
