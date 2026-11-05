# AAM — AI Mail Assistant

AAM is a proposed AI mail assistant for insurance account managers. It helps understand incoming email, prepare replies, track cases and obligations, follow approved workflows, and remind people about outstanding actions.

**Status: specification and architecture only. No application has been implemented or deployed.** The stories describe generic insurance account-management scenarios and configurable policies.

## Latest Workbuddy proposal

The [project overview deck](docs/presentations/aam-project-overview-mvp.pptx) now presents a conversation-first AM assistant that executes approved tasks. Editable diagrams show the [agent task flow](docs/architecture/diagrams/aam-workbuddy-task-flow.drawio) and [service architecture](docs/architecture/diagrams/aam-workbuddy-service-architecture.drawio).

The MVP proposal uses Spring Boot with AgentScope Java and Azure Foundry. Java task executors use PostgreSQL for persisted jobs, approval waits, context and business-step checkpoints. Temporal is deferred unless more complex long-running workflows justify it. Framework compatibility, lease-based recovery, external-action reconciliation and connector permissions still require validation.

The current [PM delivery plan and role-based Gantt](docs/planning/AAM_PM_Story_Delivery_Plan_EN.xlsx) schedules preparation from November 2, 2026, engineering foundations on November 16–20, eight agent coding packages on November 23–26, and first full real-Outlook integration acceptance on November 27. Daily merges precede that acceptance. The sprint depends on fixed contracts, shared-code ownership, working service access and a persistent task executor. DS tuning, independent technical/business QA and fixes continue through demo development/test signoff on December 15. Architecture review runs November 16–December 11 in parallel; architecture, QA and security approval gate customer SIT trial on December 22.

The workbook separates BE, FE/UX, DS, technical QA, business QA, BA, TL and PM responsibilities, with editable task dates, allocations and progress fields. Its forecast is 681.2 person-days: USD 142,500 in 2026 and an indicative USD 137,547 in 2027, totaling USD 280,047 including penetration testing and hosting/model allowances. PM and TL are half-time; TL is USD 450/day. The USD 150,000 year-end budget retains USD 7,500 unallocated. Agent coding does not add billable headcount, and earlier coding completion reserves time for validation rather than implying a staffing reduction. Production remains provisionally April 5, 2027, with handover April 9 and shared leave February 3–12.

The [earlier April plan](docs/planning/AAM_Workbuddy_Delivery_Plan_to_2027-04-09_EN.xlsx) and [March proposal](docs/planning/AAM_Workbuddy_Delivery_Plan_to_2027-03-19_EN.xlsx) are historical baselines. The deck's schedule/cost page has not been updated to this PM plan. The OpenSpec materials and earlier diagrams below describe the previous design and have not yet been reconciled with this proposal.

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
