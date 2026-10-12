## Purpose

Define the observable program changes behavior of AAM, including failure handling and human oversight for regional client coordination.

Business policy dependencies are recorded in [the decision register](../../../../../docs/product/decisions.md). Scenarios with approved policies use explicit synthetic fixtures; they do not select production business rules.

## ADDED Requirements

### Requirement: PC-01 Underwriting event and impact resolution

The system SHALL create or link a program-change case from a notice accepted under an approved underwriting-authority policy concerning a change in coverage or program support for an existing account or new business opportunity. It SHALL identify affected entities and preserve evidence, uncertainty and required next actions. The business owner defines who can issue/confirm such notices (D-17); absence of this policy SHALL hold the trigger for resolution. Mail authentication is supporting provenance, not underwriting authority.

Source stories: US-04.

#### Scenario: PC-01-P Positive acceptance

- **GIVEN** an authorized underwriting team notice identifies an affected country and program
- **WHEN** the notice is processed
- **THEN** a linked case and affected-country actions are created with source evidence

#### Scenario: PC-01-N Negative acceptance

- **GIVEN** the sender is unverified or affected entities are ambiguous
- **WHEN** processing runs
- **THEN** the case requires confirmation and no client-specific change notice is automatically released

### Requirement: PC-02 Approved communications and next steps

The system SHALL prepare account-management communications to local entities and clients/brokers for authorized approval, track acknowledgments and agreed next steps, and close only when mandatory actions are completed or waived. It SHALL not create replacement coverage or underwriting commitments.

Source stories: US-04.

#### Scenario: PC-02-P Positive acceptance

- **GIVEN** the approved notice is released and agreed actions receive completion evidence
- **WHEN** closure is evaluated
- **THEN** the case closes with approvals, communications and dispositions retained

#### Scenario: PC-02-N Negative acceptance

- **GIVEN** a draft promises replacement coverage or only sending has completed
- **WHEN** authorization or closure is evaluated
- **THEN** the unsupported commitment is blocked and unresolved actions keep the case open
