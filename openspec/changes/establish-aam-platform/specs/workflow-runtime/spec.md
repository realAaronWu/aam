## Purpose

Define the observable workflow runtime behavior of AAM, including failure handling and human oversight for regional client coordination.

Business policy dependencies are recorded in [the decision register](../../../../../docs/product/decisions.md). Scenarios with approved policies use explicit synthetic fixtures; they do not select production business rules.

## ADDED Requirements

### Requirement: WF-01 Durable waits and recovery

The system SHALL preserve business progress, waits and timers across worker restarts using one authoritative persisted business state. Scheduler replay SHALL not override newer, canceled or restored business state. Recovery SHALL re-evaluate pending work from that state without repeating accepted external actions. Versioned definitions and compatible executable artifacts required by active cases SHALL remain retrievable; incompatible or missing versions SHALL suspend affected work visibly.

Source stories: US-01, US-03, US-04, US-08.

#### Scenario: WF-01-P Positive acceptance

- **GIVEN** a country task is waiting for a reply and workers restart
- **WHEN** the reply arrives
- **THEN** the same workflow resumes with its existing task and pending deadline

#### Scenario: WF-01-N Negative acceptance

- **GIVEN** the recorded workflow version is unavailable during recovery
- **WHEN** execution is attempted
- **THEN** the case is visibly suspended for recovery and is not silently run under a different definition

### Requirement: WF-02 Reliable transitions and concurrent events

The system SHALL durably couple accepted state changes with their outgoing work, deduplicate repeated events and reject stale transitions. Reply arrival, cancellation and reminder dispatch races SHALL be resolved against current obligation state; outdated work SHALL be suppressed with an audit record.

Source stories: US-01, US-02, US-08.

#### Scenario: WF-02-P Positive acceptance

- **GIVEN** a reply satisfies an obligation before its queued reminder is dispatched
- **WHEN** the reminder rechecks state
- **THEN** it is suppressed and the satisfaction event remains authoritative

#### Scenario: WF-02-N Negative acceptance

- **GIVEN** a database failure occurs between an intended update and event publication
- **WHEN** processing recovers
- **THEN** there is neither a committed update with permanently lost work nor duplicate business transitions

### Requirement: WF-03 Business calendars and escalation

The system SHALL compute reminders, escalation and upcoming/overdue Account Manager notifications from approved workflow rules, business calendars, time zones and authorized due-date changes. It SHALL stop obsolete reminders, enforce cadence limits and show exceptions. Notification channel, lead time, audience and the effect of human follow-up on cadence SHALL be supplied by the business owner (D-14); missing values SHALL block that notification/reminder path with a configuration diagnostic rather than inventing behavior.

Source stories: US-01, US-03, US-04.

#### Scenario: WF-03-P Positive acceptance

- **GIVEN** a due date crosses a local holiday and is later rescheduled
- **WHEN** timers are updated
- **THEN** the approved business-calendar deadline applies and superseded timers do not send

#### Scenario: WF-03-N Negative acceptance

- **GIVEN** a workflow lacks an owner or required calendar rule
- **WHEN** the task becomes due
- **THEN** an explicit configuration/ownership exception is raised instead of inventing a recipient or schedule

#### Scenario: WF-03-P2 Configured Account Manager notification

- **GIVEN** an approved synthetic rule selects a notification channel and lead time
- **WHEN** an obligation enters its upcoming window and later becomes overdue
- **THEN** the configured notifications are recorded once per rule occurrence with delivery failures visible
