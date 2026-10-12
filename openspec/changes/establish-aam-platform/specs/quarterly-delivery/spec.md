## Purpose

Define the observable quarterly delivery behavior of AAM, including failure handling and human oversight for regional client coordination.

Business policy dependencies are recorded in [the decision register](../../../../../docs/product/decisions.md). Scenarios with approved policies use explicit synthetic fixtures; they do not select production business rules.

## ADDED Requirements

### Requirement: QD-01 Recurring country delivery obligations

The system SHALL create a quarterly parent case and per-country obligations for applicable local teams to send local claims-report decks to local clients, using client-specific calendars and contacts. Repeated scheduling SHALL not duplicate the account/quarter/country obligation.

Source stories: US-03.

#### Scenario: QD-01-P Positive acceptance

- **GIVEN** a quarter becomes due for three applicable countries
- **WHEN** the schedule fires
- **THEN** three owned country obligations are visible in one quarterly case

#### Scenario: QD-01-N Negative acceptance

- **GIVEN** the scheduling event repeats or one country has no authorized local contact
- **WHEN** processing runs
- **THEN** existing obligations are reused and the missing contact becomes an exception

### Requirement: QD-02 Verify local client delivery

The system SHALL remind and follow up until approved evidence establishes that the correct quarter/country deck was sent to the appropriate local client. Accepted evidence types SHALL be configured by policy; a future promise or wrong-period deck SHALL not complete the obligation.

Source stories: US-03.

#### Scenario: QD-02-P Positive acceptance

- **GIVEN** an authorized local team supplies the configured acceptable sent-deck evidence
- **WHEN** review validates client, country and quarter
- **THEN** that obligation completes and its reminders stop

#### Scenario: QD-02-N Negative acceptance

- **GIVEN** the local team promises delivery tomorrow or attaches last quarter’s deck
- **WHEN** completion is evaluated
- **THEN** the obligation remains open with the missing or incorrect evidence identified
