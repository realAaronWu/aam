## Purpose

Define the observable renewal monitoring behavior of AAM, including failure handling and human oversight for regional client coordination.

Business policy dependencies are recorded in [the decision register](../../../../../docs/product/decisions.md). Scenarios with approved policies use explicit synthetic fixtures; they do not select production business rules.

## ADDED Requirements

### Requirement: RN-01 Proactive renewal creation

The system SHALL identify eligible renewals from CRM and approved client-specific lead times. It SHALL create at most one active renewal case for an account/program/cycle and establish applicable country workstreams with owners and milestones.

Source stories: US-01.

#### Scenario: RN-01-P Positive acceptance

- **GIVEN** a account with an approved extended lead time reaches its approved earlier lead time
- **WHEN** renewal monitoring runs
- **THEN** one regional case and its required country workstreams are created

#### Scenario: RN-01-N Negative acceptance

- **GIVEN** eligibility is evaluated repeatedly or the renewal date is missing
- **WHEN** monitoring runs
- **THEN** no duplicate case is created and missing dates are surfaced for correction

### Requirement: RN-02 Pricing and terms coordination

The system SHALL track local team pricing-file submission to underwriting, outstanding submissions, received renewal terms, local team alignment and authorized terms release, retaining country, period and deliverable versions. The business owner SHALL define whether consolidation is document assembly, status coordination or commercial/numeric authorship (D-17). Until that scope and its validation rules are approved, commercial consolidation generation/release SHALL remain disabled. For any approved consolidation, applicable country inputs and approved versions SHALL remain traceable.

Source stories: US-01.

#### Scenario: RN-02-P Positive acceptance

- **GIVEN** all required country pricing submissions and terms alignments are accepted
- **WHEN** regional terms are prepared
- **THEN** the consolidation identifies its country inputs and exact approved versions

#### Scenario: RN-02-N Negative acceptance

- **GIVEN** one country is missing or revised terms supersede an approved version
- **WHEN** release is requested
- **THEN** the incomplete or stale consolidation is blocked or explicitly routed for an authorized exception

### Requirement: RN-03 Negotiation rounds and finalization

The system SHALL track negotiation rounds, revised terms, stakeholder actions and confirmed renewal outcomes until finalization. It SHALL not invent or autonomously accept commercial terms. Finalization SHALL require business-approved confirmation evidence (D-12/D-17) and disposition of remaining mandatory work; an unset confirmation rule SHALL block finalization.

Source stories: US-01.

#### Scenario: RN-03-P Positive acceptance

- **GIVEN** a revised round receives required approvals and final confirmation
- **WHEN** finalization is requested
- **THEN** the outcome and accepted term versions are recorded and obsolete follow-ups stop

#### Scenario: RN-03-N Negative acceptance

- **GIVEN** a broker asks for a discount or merely acknowledges receipt
- **WHEN** the message is interpreted
- **THEN** a negotiation action is proposed without accepting the discount or closing the case
