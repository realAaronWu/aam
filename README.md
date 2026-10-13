# AAM — AI Mail Assistant

AAM is a proposed AI mail assistant for insurance account managers. It helps understand incoming email, prepare replies, track cases and obligations, follow approved workflows, and remind people about outstanding actions.

**Status: specification and architecture only. No application has been implemented or deployed.** The stories describe generic insurance account-management scenarios and configurable policies.

## Scope

- Email understanding, account/case matching and reviewed response drafting.
- Renewal coordination, periodic report delivery and insurance program changes.
- Case status, evidence-linked obligations and follow-up reminders.
- AM-authored SOPs, scoped external connections and mandatory input-readiness predicates.
- Small reviewed example libraries for tuning intent recognition and response quality.
- Controlled automation with explicit approval, factual checks and delivery safeguards.

The proposed architecture uses four deployables: portal/API, intake/synchronization, workflow/agent workers, and delivery workers. Managed Azure services provide hosting, storage, identity and execution. Models propose actions; application policy controls authorization and sending.

## Review the design

1. [User stories](docs/product/user-stories.md) and [scope assumptions](docs/product/sources.md).
2. [Capability specifications](openspec/changes/establish-aam-platform/specs/): 12 capabilities, 40 requirements and 96 scenarios.
3. [Architecture and data-flow diagrams](openspec/changes/establish-aam-platform/design.md), including example ingestion and retrieval.
4. [Roadmap](docs/planning/roadmap.md) and [implementation tasks](openspec/changes/establish-aam-platform/tasks.md): 56 planned tasks.
5. [Testing strategy](docs/testing/strategy.md), [traceability](docs/traceability.json) and [decisions](docs/product/decisions.md).

## Validate the specifications

Requires Node.js 20.19 or newer. OpenSpec 1.8.0 is pinned in the lockfile.

```sh
npm ci --ignore-scripts
npm run spec:validate
npm run spec:status
```

The active change is `establish-aam-platform`. Its planning artifacts are complete; implementation tasks remain unchecked. Structural validation does not establish model accuracy, provider compatibility or production readiness.

See [CONTRIBUTING.md](CONTRIBUTING.md) for the specification-driven workflow. Main specs under `openspec/specs/` remain empty until implemented and validated behavior is promoted.
