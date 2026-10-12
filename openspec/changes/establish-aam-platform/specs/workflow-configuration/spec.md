## Purpose

Define the observable workflow configuration behavior of AAM, including failure handling and human oversight for regional client coordination.

## ADDED Requirements

### Requirement: CFG-01 Versioned client workflow packages

The system SHALL publish reviewed workflow types defining steps, schemas, completion-policy slots, allowed actions and source SOP references, separately from versioned per-client parameters. Active cases SHALL pin both versions unless an authorized, audited migration is applied. Publication/activation SHALL reject missing required business decisions rather than supplying unstated evidence, authority or recipient rules.

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

The system SHALL support composing collect, request, wait, evaluate, approve, remind, escalate and consolidate steps through documented contracts. New workflow types SHALL declare schemas, tools, authorization and tests. Existing workflow behavior SHALL remain unchanged unless its version is explicitly migrated.

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
