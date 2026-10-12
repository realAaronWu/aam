# Extension workflow acceptance outlines

These proposed workflows remain in scope for product evolution but are deferred from the initial priority-workflow delivery. Each outline is the starting contract for a future OpenSpec change, not a claim that the feature is implemented. Use the same identity, evidence, approval and completion invariants as the initial platform.

## US-09 Quarterly consolidated reporting

- **Proposed change:** `add-quarterly-report-consolidation`.
- **Breakdown:** establish reporting-source/export contracts; track approved reporting-source access gaps; collect non-enabled-country commentary; assemble the consolidated report; validate coverage and approve release.
- **Inputs and owners:** applicable country inventory, approved claims-reporting exports, local team commentary; Reporting Team supplies reports and Regional AM owns consolidation.
- **Positive acceptance:** one enabled and one non-enabled country both contribute the correct quarter's data/commentary, with source versions and a reviewed regional report sent to authorized client/broker recipients.
- **Negative acceptance:** missing country, mixed quarters or stale report exports remain visible and block an apparently complete consolidation unless an authorized exception is recorded. The system cannot imply reporting-source access completed merely because it requested it.
- **Verification:** connector contract tests plus a mixed-source, mixed-country assembly/review/release journey. No screenshot-based integration is assumed until supported export access is assessed.

## US-10 Health talk tracker

- **Proposed change:** `add-health-talk-tracking`.
- **Breakdown:** define policy-year/country applicability; create local team collection obligations; validate utilisation/event submissions; consolidate and approve regional update.
- **Positive acceptance:** required countries submit policy-year activity, including an explicit valid zero-activity response, and the approved summary links each country source.
- **Negative acceptance:** silence is not zero utilisation; a prior policy-year update does not satisfy the current year. Follow-up remains active for missing inputs.
- **Verification:** collection workflow and period-validation tests, plus evidence-linked summary review.

## US-11 Pool report issuance

- **Proposed change:** `add-pool-report-issuance`.
- **Breakdown:** configure pool closing date/SLA; create Reporting Team obligation; track report versions; draft Regional AM executive summary; approve and issue.
- **Positive acceptance:** the applicable report and summary are reviewed and issued to authorized recipients within the configured SLA calendar.
- **Negative acceptance:** an illustrative interval cannot override a specific SLA; missing or superseded pool report blocks unsupported summary claims and release.
- **Verification:** SLA timer boundary tests and version-bound report/summary approval tests.

## US-12 Client review meetings

- **Proposed change:** `add-client-review-meetings`.
- **Breakdown:** configure monthly/quarterly/semiannual cadence; collect preparation materials; record confirmed meeting outcomes; assign and follow action items.
- **Positive acceptance:** confirmed actions have responsible owners, due dates and source meeting references, and later replies update the correct obligations.
- **Negative acceptance:** a tentative discussion is not an agreed commitment; no owner or unresolved due date is an exception, and sending minutes does not complete the actions.
- **Verification:** meeting-to-action extraction with authorized confirmation and durable follow-up scenarios.

## US-13 SLA and service reviews

- **Proposed change:** `add-service-reviews`.
- **Breakdown:** configure country metrics and cadence; request local team results; validate units/periods; consolidate, review and release.
- **Positive acceptance:** all required country results use the correct definitions and review period, with approved exceptions explicitly identified.
- **Negative acceptance:** missing values cannot become zeros; incompatible denominators or mixed periods cannot silently produce a regional aggregate.
- **Verification:** metric-schema/aggregation tests and country completeness/approval journey.

## US-14 Renewal timeline planning

- **Proposed change:** `add-renewal-timeline-planning`.
- **Breakdown:** draft milestones; request underwriting team agreement; obtain local team consensus; version agreed calendar; approve client/broker release and explicitly connect it to active renewal cases.
- **Positive acceptance:** the released timeline is the version agreed by required parties and resulting milestones are traceable.
- **Negative acceptance:** revised milestones invalidate earlier agreement where material; absent consensus does not become approval through elapsed time, and active-case timers are not silently rewritten.
- **Verification:** multi-party approval/version tests and explicit workflow migration/rescheduling tests.

## US-15 Renewal meetings

- **Proposed change:** `add-renewal-meeting-coordination`.
- **Breakdown:** prepare current renewal updates and terms; review strategy with client/broker; confirm meeting actions; attach them to the proper renewal round.
- **Positive acceptance:** materials reference the current permitted term versions and confirmed actions enter the renewal case with owners/deadlines.
- **Negative acceptance:** draft strategy cannot authorize commercial concessions; stale terms cannot be represented as the current approved position, and meeting completion does not finalize renewal.
- **Verification:** term-version/recipient checks and meeting-to-renewal-action acceptance tests.
