# Decisions, owner questions and activation gates

Architecture is an engineering proposal. Business owners define the stories and their operating policy. The questions below deliberately have no engineering-authored business answers. Synthetic fixtures are permitted for development; they do not approve production behavior. An unanswered question blocks the affected behavior, not unrelated engineering work. Answers that change contracts require a reviewed spec/task update.

## Architecture and platform inputs

| ID | Decision/input | Treatment | Owner and gate |
| --- | --- | --- | --- |
| D-01 | Specialist implementation | ADR-01/12/15: TypeScript only; Next.js/Node.js, Zod and Deep Agents TypeScript with LangChain tools and LangGraph attempt-local execution; AAM owns readiness, authorization and long-lived business state | Engineering, T11 |
| D-02 | Business persistence | ADR-02/06: PostgreSQL owns cases, waits, timers and bounded jobs with leases/fences; external orchestration deferred | Engineering/platform, before T03.1 |
| D-03 | Initial action mode | Review-assisted; automatic production permissions require D-13 and qualification | Business owner, before action activation |
| D-04 | CRM API/fields/write-back | Read contract first; field ownership and write-back mappings require CRM owner. Write-back is optional and does not block read-only journeys | CRM owner, T06.1/T06.2 |
| D-08 | Region, residency, retention, classification | Cover all stores, model requests, indexes, logs, scheduler and backups; no production data without approval | Data/platform owners, T01.3/T24.1 before data use |
| D-09 | Workload, quotas, SLO/RPO/RTO | Include Graph/mailbox/Foundry limits, active timers, queue age and burst load; strategy targets are proposals | Service owner, T01.3 capacity spike and T23/T25 qualification |
| D-10 | Models and gate thresholds | Engineering supplies measurement/confidence methods; business/AI owners approve acceptable limits and categories | AI/business owners, T21/T26 |
| D-11 | Extension priorities | US-09–US-15 remain separate future changes | Business owner |
| D-15 | AKS operating model and CI host | ADR-07 chooses polling; platform chooses portal ingress, supported upgrade/patch cadence, maintenance owner and windows, tested autoscaling metric, PDB/drain budget and image scanning. Choose GitHub Actions or Azure DevOps before implementing provider-specific CI | Platform owner, CI host before T01.2; dev/test access before T01.3; production acceptance T23 |

## Open questions for the business owner

All rows below are **OPEN**. A fixture choosing one option is not an answer. Record the owner, decision date, approval reference and affected versions when resolved.

| ID | Questions to resolve | Affected behavior and activation gate |
| --- | --- | --- |
| D-05 | Which shared/personal mailboxes and folders can AAM observe? Which identities can draft/send? What legitimate human activity can occur outside that observation boundary? | IN-01/IN-04/OP-04; no production access before approved inventory |
| D-06 | Which calendars, lead times, account-specific lead-time rules, cadence caps and due-date overrides apply by client? | Lead times are configurable policy, not a universal fixed interval; RN-01/WF-03 activation |
| D-07 | For quarterly local reporting, what proves the correct deck was sent to the local client, and who may accept or waive it? | QD-02; a specialized instance of D-12, no preselected evidence types |
| D-12 | For every renewal/program/quarterly obligation, what evidence is observable, through which source, and who can confirm completion or waive missing evidence? How should invisible submissions be handled? | CS-02/RN/QD/PC completion and package activation |
| D-13 | Which action categories may automate, require approval, or remain prohibited? Who can approve each action and precondition? May factual warnings be acknowledged, by whom, with what evidence? What are expiry/freshness limits? | AU/AI-03/QA-02 activation |
| D-14 | How and when should AMs receive upcoming/overdue notifications? Which channels, audiences and cadence? Does a human follow-up defer/cancel a reminder? | WF-03 activation |
| D-16 | How are proposed obligations promoted and owners/dates confirmed? How should legitimate multi-client mail and ambiguous local team messages be handled? How are to/cc/bcc, reply-all and new contacts computed? Which manual case creation, attestation, takeover/mute and schedule overrides are needed? What defines relevant versus unrelated mail? | affected case commands, recipient policy and intake paths stay unconfigured |
| D-18 | Who may author/publish SOPs, select each connection and attest to missing facts? Which input predicates are mandatory, who may revise them, what freshness/source-precedence rules apply, and which changes may migrate active cases? | CFG-04/AI-05/CX-04; definition activation and predicate changes require declared authority |
| D-17 | Does consolidation mean collecting documents/status or authoring commercial/numeric terms? What confirms renewal finalization? Who is authoritative for a program-change notice and what confirmation is acceptable? | commercial consolidation/finalization and program trigger activation |

Engineering provides typed policy slots, validation, provenance, access controls and explicit holds. It does not assume that mail authentication proves underwriting authority, that Bcc is always prohibited, that every internal reminder may auto-send, or that every AM can attest/override. Those would be business decisions. Mandatory security controls remain enforced regardless of the selected policy.
