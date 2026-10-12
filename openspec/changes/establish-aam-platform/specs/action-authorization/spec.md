## Purpose

Define the observable action authorization behavior of AAM, including failure handling and human oversight for regional client coordination.

Business policy dependencies are recorded in [the decision register](../../../../../docs/product/decisions.md). Scenarios with approved policies use explicit synthetic fixtures; they do not select production business rules.

## ADDED Requirements

### Requirement: AU-01 Explicit action policy

The system SHALL evaluate configured action type, account certainty, recipient authority, evidence, request freshness and risk independently of knowledge-match status. Production action classes, approver authorities, recipient computation and exceptions SHALL come from approved business policy (D-13/D-16); absent policy SHALL deny dispatch. Automatic actions SHALL require category-specific release evidence. Deterministic templates and model drafts SHALL be distinguishable so their gates can differ without bypassing shared authorization. Initial delivery remains review-assisted; no engineering-defined action class gains automatic permission.

Source stories: US-02, US-07.

#### Scenario: AU-01-P Positive acceptance

- **GIVEN** a validated routine reminder has an active approved automation policy
- **WHEN** authorization runs
- **THEN** the policy decision and allowed command are recorded

#### Scenario: AU-01-N Negative acceptance

- **GIVEN** under the initial review-assisted policy, knowledge is sufficient but the draft releases renewal terms or the account is ambiguous
- **WHEN** authorization runs
- **THEN** automatic dispatch is denied and the required review is created

### Requirement: AU-02 Exact approval and freshness

The system SHALL bind approval to a dependency set: exact subject/body, to/cc/bcc sets, attachment content hashes, request disposition, referenced obligation/fact/term versions, pinned workflow/parameter versions, policy version, approver authority and expiry. Any changed dependency, revoked authority or expired approval SHALL invalidate dispatch permission; an unrelated case event SHALL not. Server-generated dependencies SHALL not rely solely on the writer claim map. The same policy checks SHALL run in the approval API and immediately before dispatch. Commands with stale, absent or incomplete dependency sets SHALL be held.

Source stories: US-02, US-07.

#### Scenario: AU-02-P Positive acceptance

- **GIVEN** an authorized reviewer approves an unchanged current draft
- **WHEN** dispatch checks succeed
- **THEN** the approved content is the content submitted

#### Scenario: AU-02-N Negative acceptance

- **GIVEN** the recipient changes or the obligation is canceled after approval
- **WHEN** dispatch is attempted
- **THEN** the command is blocked and a fresh decision is required

#### Scenario: AU-02-P2 Unrelated country event

- **GIVEN** a current approval refers only to country A and country B sends an unrelated update
- **WHEN** dispatch revalidates the recorded dependency set
- **THEN** the approval remains valid although the overall case version advanced

#### Scenario: AU-02-N2 Referenced fact superseded

- **GIVEN** a term version referenced by the approved draft is superseded or its approval expires
- **WHEN** dispatch is attempted
- **THEN** the command is blocked with the specific dependency or expiry failure

### Requirement: AU-03 Controlled delivery and uncertainty

The system SHALL execute only persisted authorized commands through scoped identities and record submission separately from delivery or business completion. Provider drafts and attempts SHALL be correlated to a stable command before sending. Duplicate commands SHALL not intentionally duplicate sends. Ambiguous draft-creation or send outcomes SHALL require reconciliation and, if unresolved, operator review rather than blind retries. Late provider failures SHALL update communication status without fabricating completion or automatically resending.

Source stories: US-07, US-08.

#### Scenario: AU-03-P Positive acceptance

- **GIVEN** a recorded command is redelivered after provider acceptance
- **WHEN** the worker processes it again
- **THEN** the existing outcome is reconciled and no new send is intentionally issued

#### Scenario: AU-03-N Negative acceptance

- **GIVEN** the provider times out after possibly accepting a message
- **WHEN** delivery resumes
- **THEN** the command remains outcome-uncertain until reconciled and is not reported delivered or blindly resent

#### Scenario: AU-03-N2 Late failure after acceptance

- **GIVEN** a submitted message is later associated with a verified nondelivery report
- **WHEN** reconciliation runs
- **THEN** the communication records failure and an exception without automatic resend or business completion
