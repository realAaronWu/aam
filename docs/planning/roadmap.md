# AAM delivery roadmap

This is dependency order, not a calendar estimate. Stable task IDs do not imply execution order. Each task's explicit milestone and dependencies in [tasks.md](../../openspec/changes/establish-aam-platform/tasks.md) are mirrored in [traceability.json](../traceability.json). Business decisions remain activation gates; synthetic policies do not satisfy them.

| Milestone | Scope | Exit condition |
| --- | --- | --- |
| M0 Reviewed planning | Specs, decisions and acceptance contracts | Engineering revisions reviewed; business questions assigned. Unresolved owner decisions do not become assumed answers |
| M1 Foundation | T01–T09 foundations (CRM read only), T10.1, T16.1/.2, T17.1/.3, T21.1, T22.1 | Scoped dev/test environment, early corpus, authoritative state/waits/timers, mail observation, parameter publication and case view work with explicit synthetic policy |
| M2 Deterministic quarterly slice | T14.1/.2, T15.1/.2, T19.1/.2 | Review-assisted templated follow-up, approved-policy evidence processing, restart and human-reply suppression pass in isolated provider tests |
| M3 Intelligence and other journeys | T06.3, T11–T13, T14.3, T17.4, T10.2, T18.1/.2/.3, T20.1/.2, T21.2 | Deep Agents, scoped connections, AM SOP authoring and mandatory output-readiness checks tested alongside renewal/program journeys |
| M4 Production qualification | T06.2, T17.2, T22.2, T23.1/.2, T24.1, T25.1/.2 | Qualified release scope, data controls, cold deploy/restore, real CI and completed independent correctness/delivery review |
| M5 Scoped automation | T26.1/.2 | Business-approved categories satisfy their own evidence gates; shadow/canary, disablement and rollback verified |

M2 has **no dependency** on model drafting/validation, AI Search, CRM write-back, workflow migration or the synthetic health-talk extension. Its template commands still require effective mailbox scope, exact approval, observation freshness, safe delivery and persistent waits. Notification/action policy remains the business owner's decision; deterministic does not mean automatically permitted.

Provision environment/identity foundations and start corpus preparation in M1. Add Search and model resources when their M3 consumers need them. Production hardening is M4, not the first opportunity to exercise Azure. A dev/test fixture pass cannot satisfy provider qualification.

Initial production activation is gated by M4 qualification and applicable business decisions. If an optional feature such as CRM write-back is excluded from a release, split its requirements/tasks into a separate active change with explicit scope before qualifying that release. Do not tick an unbuilt feature or bypass its scenario to shorten the plan. Automatic categories can use different evidence profiles, but share security, recovery and delivery gates.

## Extensions and slicing

US-09 consolidated reporting; US-10 health talks; US-11 pool reports; US-12 client meetings; US-13 SLA reviews; US-14 renewal timeline agreement; US-15 renewal meetings each receive a future change and source-specific acceptance. Their outlines remain in [extension workflows](../product/extension-workflows.md).

The initial change presents the proposed first-release contract. For milestone implementation changes, move each requirement to exactly one owning change, preserve IDs and maintain scenario closing ownership. Archive only implemented, validated scope. There must never be conflicting copies or future behavior represented as shipped.
