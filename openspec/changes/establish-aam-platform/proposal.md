## Why

Account Managers manually coordinate renewals, local reporting and program changes across clients, brokers, underwriting and local insurance teams. AAM must interpret correspondence and maintain reliable obligations, deadlines and evidence while preserving human authority over sensitive actions.

## What Changes

- Keep the implementation TypeScript throughout: Next.js portal/API, shared Zod contracts and Node.js workers with Deep Agents TypeScript; reuse reviewed demo modules and selected reliability patterns without a second-language service.
- Establish a case-centered platform covering generic account-management stories and three priority workflows.
- Resolve client and case context, track correspondence across threads, and provide current summaries.
- Use Deep Agents for dynamic SOP interpretation and bounded data gathering, with application-owned output readiness, guarded writing and action authorization.
- Let authorized AMs publish SOP steps, branches and output predicates using approved connections and tools, including D365 CRM, Outlook and knowledge sources.
- Tune interpretation and drafting with small reviewed workflow-example files shipped in the existing agent bundle, deterministic local selection and separate held-out evaluation; no separate example service or vector index.
- Keep long-lived cases, waits and timers in one authoritative business store; use bounded technical execution.
- Separate AM-authored workflow definitions within registered capability schemas from bounded client parameters; add category-specific evaluation gates and managed Azure deployment.
- Deliver deterministic quarterly tracking before model-generated responses; preserve unresolved business decisions as activation gates.
- Enforce trusted context, factual validation, evidence-based case closure and restart-safe execution.

## Capabilities

### New Capabilities

- `mail-ingestion`: Recoverable inbound/outbound observation, attachments and CRM synchronization.
- `account-context`: Account and case resolution, authorization and immutable context.
- `case-tracking`: Obligations, evidence, conversation links and current summaries.
- `agent-mail-response`: Typed specialists, scoped retrieval, evidence and factual validation.
- `action-authorization`: Automatic-action policy, exact approvals and controlled delivery.
- `workflow-runtime`: Durable waiting, idempotent transitions and recovery.
- `renewal-monitoring`: Country submissions, terms release and negotiation tracking.
- `quarterly-delivery`: Recurring local claims-deck delivery tracking.
- `program-changes`: Underwriting-triggered impact, communications and next actions.
- `workflow-configuration`: Versioned packages and extensible workflow primitives.
- `operations-security`: Access control, operator recovery, managed deployment and audit.
- `quality-evaluation`: Evidence-based evaluation and controlled release.

### Modified Capabilities

None. This is a new repository; no implemented main specifications exist.

## Impact

Future integrations: Microsoft 365, Sales CRM and approved knowledge/reporting sources. This change proposes behavior, architecture and implementation tasks; it does not deploy services, migrate live data or send mail. Product stories and assumptions are recorded in `docs/product/`.

## Non-goals

Autonomous underwriting or negotiation commitments; unrestricted agents; self-hosted backend infrastructure; immediate implementation of every deferred reporting workflow. Those workflows remain explicit extension stories, not discarded requirements. Initial delivery is review-assisted; automatic sending requires separately evidenced policy activation.
