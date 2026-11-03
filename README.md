# AAM — AI Mail Assistant

AAM is a proposed AI mail assistant for insurance account managers. It helps understand incoming email, prepare replies, track cases and obligations, follow approved workflows, and remind people about outstanding actions.

**Status: specification and architecture only. No application has been implemented or deployed.** The stories describe generic insurance account-management scenarios and configurable policies.

## Latest Workbuddy proposal

The [project overview deck](docs/presentations/aam-project-overview-mvp.pptx) now presents a conversation-first AM assistant that executes approved tasks. Editable diagrams show the [agent task flow](docs/architecture/diagrams/aam-workbuddy-task-flow.drawio) and [service architecture](docs/architecture/diagrams/aam-workbuddy-service-architecture.drawio).

This proposal evaluates AgentScope Java, Temporal and Azure Foundry. Framework compatibility, recovery behavior and connector permissions still require validation. The [revised delivery plan](docs/planning/AAM_Workbuddy_Delivery_Plan_to_2027-04-09_EN.xlsx) and the deck target production on April 5, 2027 and handover on April 9, with longer agent tuning and a dedicated release buffer. The base estimate is 534.3 person-days and USD 207,348 including penetration testing. The [March release proposal](docs/planning/AAM_Workbuddy_Delivery_Plan_to_2027-03-19_EN.xlsx) is retained only as the cost comparison baseline. The OpenSpec materials and earlier diagrams below describe the previous design and have not yet been reconciled with this proposal.

## Scope

- Email understanding, account/case matching and reviewed response drafting.
- Renewal coordination, periodic report delivery and insurance program changes.
- Case status, evidence-linked obligations and follow-up reminders.
- AM-authored SOPs, scoped external connections and mandatory input-readiness predicates.
- Small reviewed example libraries for tuning intent recognition and response quality.
- Controlled automation with explicit approval, factual checks and delivery safeguards.

The earlier OpenSpec design uses Java/Spring Boot for API, cases, workflow jobs, connections, readiness, approvals and delivery. Next.js remains the portal; a bounded TypeScript Deep Agents companion handles model execution under Java-issued scope and phase grants. That design uses four deployment roles with managed PostgreSQL/Blob and one Java-owned business/job authority, deferring additional orchestration services. Models propose; Java application policy controls authorization and sending.

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
