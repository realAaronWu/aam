## Purpose

Define the observable account context behavior of AAM, including failure handling and human oversight for regional client coordination.

Business policy dependencies are recorded in [the decision register](../../../../../docs/product/decisions.md). Scenarios with approved policies use explicit synthetic fixtures; they do not select production business rules.

## ADDED Requirements

### Requirement: CX-01 Account and case resolution

The system SHALL resolve client, case and country workstream using authorized identifiers, policy/program references, participants, content and business period. Neither a broker nor local team address alone SHALL establish a client. Ambiguous, conflicting or multiple-account matches SHALL remain explicit before client-specific retrieval or outbound action. The response/linking policy for legitimate multi-client requests is a business-owner decision (D-16); until configured they SHALL be held for authorized resolution without mixing restricted contexts.

Source stories: US-02.

#### Scenario: CX-01-P Positive acceptance

- **GIVEN** one broker serves clients A and B and the email identifies policy B
- **WHEN** resolution executes
- **THEN** the message is associated with B using recorded evidence

#### Scenario: CX-01-N Negative acceptance

- **GIVEN** an email has conflicting client references
- **WHEN** resolution executes
- **THEN** no customer-specific answer is sent and an authorized reviewer receives the candidate matches

#### Scenario: CX-01-N2 Internal-party ambiguity

- **GIVEN** one local team serves two active renewals and sends an unidentified pricing file
- **WHEN** resolution runs
- **THEN** the system retains candidate workstreams and cannot retrieve one client by guessing from sender alone

#### Scenario: CX-01-N3 Multi-client policy absent

- **GIVEN** an email explicitly concerns two clients and no multi-client handling policy is approved
- **WHEN** processing begins
- **THEN** the message is held for resolution and cannot create a combined privileged draft

### Requirement: CX-02 Immutable authorized context

The system SHALL bind each processing run to an immutable snapshot of the current message, authorized account/case, relevant case version, history, facts and workflow version. Specialist inputs SHALL preserve authoritative fields. Data embedded in messages or retrieved documents SHALL not change roles, scope or tool permissions.

Source stories: US-02, US-07.

#### Scenario: CX-02-P Positive acceptance

- **GIVEN** a resolved case has authorized history and source facts
- **WHEN** specialists process its snapshot
- **THEN** all stages retain the same current message and account identifiers with traceable inputs

#### Scenario: CX-02-N Negative acceptance

- **GIVEN** a delegated input changes the current email or a document instructs retrieval from another client
- **WHEN** the stage is invoked
- **THEN** the boundary rejects altered context or unauthorized access and produces no executable action

### Requirement: CX-03 Explicit incomplete context

The system SHALL distinguish the current inbound message from prior correspondence and disclose omitted or truncated context internally. An incomplete current request SHALL not be classified as fully answered. Earlier outbound text SHALL not establish policy truth or prove an operation occurred.

Source stories: US-02.

#### Scenario: CX-03-P Positive acceptance

- **GIVEN** older history exceeds the context budget but the current message is complete
- **WHEN** context is assembled
- **THEN** recent ordered history and truncation metadata are provided while original records remain available

#### Scenario: CX-03-N Negative acceptance

- **GIVEN** the current body is truncated or an earlier reply asserted an unrecorded approval
- **WHEN** drafting runs
- **THEN** the system abstains from a complete answer or unsupported approval claim and routes the issue to review

### Requirement: CX-04 Authorized external connections

The system SHALL let integration administrators register versioned connections and let authorized workflow publishers select permitted tools from them. Credentials SHALL remain outside model inputs. Each read SHALL enforce current connection, actor and account scope, including child agents and cached results, and return provenance, freshness and explicit completeness/status. Connection revocation SHALL override workflow pins. A denied, failed or partially paginated read SHALL not become successful complete evidence. Dynamic tool discovery SHALL not grant unregistered operations. Agent tools SHALL not directly send mail or execute business writes.

Source stories: US-02, US-06, US-08.

#### Scenario: CX-04-P Scoped connected reads

- **GIVEN** a workflow binds approved CRM, mail and knowledge operations for one account
- **WHEN** the main agent or an authorized child requests data
- **THEN** adapters enforce the same account/field scope and return source versions, freshness and completeness without exposing credentials

#### Scenario: CX-04-N Permission widening or revocation

- **GIVEN** a tool request changes the account, a connection was revoked or a child requests an unregistered operation
- **WHEN** execution or cache access is attempted
- **THEN** it is denied and no restricted data or direct external write is released

#### Scenario: CX-04-P2 Complete collection evidence

- **GIVEN** an authorized provider returns multiple pages for an approved record set
- **WHEN** all required pages and membership checks complete
- **THEN** the result records the complete set and its source/query provenance for readiness evaluation

#### Scenario: CX-04-N2 Partial or failed source read

- **GIVEN** a source times out, returns only one page, provides stale data or an MCP schema changes unexpectedly
- **WHEN** evidence is normalized
- **THEN** unavailable, partial, stale or incompatible state remains explicit and cannot satisfy a complete-current-input requirement
