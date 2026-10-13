## Purpose

Define the observable workflow configuration behavior of AAM, including failure handling and human oversight for regional client coordination.

## ADDED Requirements

### Requirement: CFG-01 Versioned client workflow packages

The system SHALL publish reviewed workflow definitions within registered capability schemas, defining SOP instructions, output readiness predicates, steps, completion-policy slots, allowed tool/connection bindings, actions and source references, separately from versioned per-client parameters. Active cases SHALL pin both versions unless an authorized, audited migration is applied. Publication/activation SHALL reject missing required business decisions rather than supplying unstated evidence, authority or recipient rules.

Source stories: US-06.

#### Scenario: CFG-01-P Positive acceptance

- **GIVEN** a reviewed workflow type publishes a compatible version two
- **WHEN** a new case starts
- **THEN** it pins type version two while existing cases retain their recorded type and parameter versions

#### Scenario: CFG-01-N Negative acceptance

- **GIVEN** an invalid package has no completion rule or publication would implicitly alter active approvals
- **WHEN** publication or migration is requested
- **THEN** validation blocks it until corrected or explicitly resolved

### Requirement: CFG-02 Composable extensions and compatibility

The system SHALL support composing collect, request, wait, evaluate, approve, remind, escalate and consolidate steps through documented contracts. New workflow definitions SHALL declare compatible schemas, tools, authorization, output prerequisites and tests; new capability code remains engineering-owned. Existing workflow behavior SHALL remain unchanged unless its version is explicitly migrated.

Source stories: US-06.

#### Scenario: CFG-02-P Positive acceptance

- **GIVEN** a health-talk collection prototype uses existing primitives
- **WHEN** it is validated in a sandbox
- **THEN** its new package runs without changing the renewal definition

#### Scenario: CFG-02-N Negative acceptance

- **GIVEN** an extension requests an unregistered tool or incompatible event schema
- **WHEN** it is loaded
- **THEN** validation rejects it before any external action and existing cases remain operational

### Requirement: CFG-03 Bounded client parameter editing

The system SHALL let an authorized configuration administrator edit client parameters through a validated, audited interface within the published workflow type schema. A parameter revision SHALL not introduce steps, tools, permissions or new authority. New cases SHALL pin the selected revision; active cases SHALL keep their existing revision unless explicitly migrated. Allowed values and administration authority SHALL follow approved business policy.

Source stories: US-06.

#### Scenario: CFG-03-P Positive acceptance

- **GIVEN** a permitted cadence parameter is revised within its approved limits
- **WHEN** the administrator publishes the revision
- **THEN** new cases can pin it and active cases retain their prior revision

#### Scenario: CFG-03-N Negative acceptance

- **GIVEN** a parameter payload attempts to add a tool or modify the authority schema
- **WHEN** publication is requested
- **THEN** validation rejects it without changing active cases

### Requirement: CFG-04 Account manager authored dynamic SOPs

Authorized account managers SHALL be able to describe and revise SOP steps, branches, registered tool/connection selections and named-output predicates without an application code deployment. Natural-language interpretation SHALL produce a draft with a readable preview, unresolved questions and positive/missing-input simulations before authorized publication. Definitions SHALL use registered schemas/capabilities and SHALL not grant permissions, execute arbitrary code or weaken mandatory organizational controls. Publication SHALL retain versioned instructions, predicate contracts and resource bindings; active cases SHALL change only through explicit migration. Retrieved documents and inbound messages SHALL not publish definitions.

Source stories: US-06.

#### Scenario: CFG-04-P Publish a configured renewal SOP

- **GIVEN** an authorized AM describes a renewal sequence and selects existing allowed CRM and knowledge tools
- **WHEN** its readable definition and input-sufficiency simulations pass and publication is authorized
- **THEN** new cases can use the SOP, tool bindings and predicates without a code deployment

#### Scenario: CFG-04-N Invalid or overprivileged definition

- **GIVEN** a draft includes an undefined predicate, empty mandatory-input contract, unsupported connector or permission beyond the publisher's authority
- **WHEN** validation or publication runs
- **THEN** publication is rejected with actionable diagnostics and no credentials or extra tools are granted

#### Scenario: CFG-04-P2 Dynamic adaptation within the SOP

- **GIVEN** a pinned SOP permits collecting a missing quote and reconsidering revised terms
- **WHEN** new evidence changes the case's next useful action
- **THEN** the agent can choose the permitted next collection action while readiness and approval rules remain enforced

#### Scenario: CFG-04-N2 No silent active workflow change

- **GIVEN** an AM publishes a new SOP or an inbound email instructs the agent to remove a required quote
- **WHEN** an older active case resumes
- **THEN** it retains its approved definition unless explicitly migrated and untrusted instructions cannot remove prerequisites
