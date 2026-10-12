# AAM user story catalogue

Status: proposed generic insurance account-management scenarios; no application behavior is implemented.

## Scope

Initial delivery includes US-01 through US-08 in review-assisted operation, followed by separately qualified low-risk automation. US-09 through US-15 are retained extension stories; they require future OpenSpec changes and are not silently included in the initial implementation checklist. Their individual breakdowns and positive/negative acceptance outlines are in [extension workflows](extension-workflows.md).

## US-01 Renewal monitoring and coordination

- **Scope:** Initial.
- **Story:** As Account Manager, I want to proactively monitor renewals, coordinate country submissions and terms, and follow actions until renewal is finalized, so that timely, consistent renewals without manual chasing.
- **Trigger and timing:** CRM renewal date and approved lead time; with account-specific lead times configured by the business owner.
- **Typical workflow:** Create country workstreams → remind local teams to submit pricing files to underwriting team → follow outstanding submissions → review terms → obtain local team alignment for release → consolidate regional terms → coordinate negotiation rounds → record final outcome.
- **Acceptance contracts:** CS-01, CS-02, CS-03, CS-04, IN-03, RN-01, RN-02, RN-03, WF-01, WF-02, WF-03 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-02 Intelligent email handling

- **Scope:** Initial.
- **Story:** As Account Manager, I want to understand current client or broker requests, resolve the right account, apply its SOP and prepare the appropriate response, so that less reading and coordination with human oversight.
- **Trigger and timing:** Incoming authorized client/broker email.
- **Typical workflow:** Analyze sender, recipients, subject, body and authorized history → resolve account/case → retrieve CRM and approved SOP → identify action/owner/due date → draft or route ambiguity → link subsequent correspondence.
- **Acceptance contracts:** CX-01, CX-02, CX-03, AU-01, AU-02, AI-01, AI-02, AI-03, AI-04, CS-01, CS-03, CS-04, IN-01, IN-02, IN-03, IN-04, OP-01, QA-01, WF-02 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-03 Quarterly local claims deck delivery

- **Scope:** Initial.
- **Story:** As Account Manager, I want to remind each local team to send its local quarterly claims-report deck to the local client and track completion, so that no local reporting obligation is missed.
- **Trigger and timing:** Quarterly, using client-specific calendar and applicable countries.
- **Typical workflow:** Create local delivery obligations → remind local teams → follow up → accept evidence of correct country/quarter/client delivery.
- **Acceptance contracts:** CS-02, QD-01, QD-02, WF-01, WF-03 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-04 Program changes

- **Scope:** Initial.
- **Story:** As Account Manager, I want to coordinate approved communications and next actions when an authorized underwriting notice changes available coverage or program support, so that affected parties understand and act on a confirmed program change.
- **Trigger and timing:** Ad hoc underwriting notice concerning an existing country or new business opportunity.
- **Typical workflow:** Verify notice and impact → prepare account-management communication to local entity and client/broker → obtain approval → inform parties → align and track next steps.
- **Acceptance contracts:** CS-02, PC-01, PC-02, WF-01, WF-03 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-05 Current case overview

- **Scope:** Initial.
- **Story:** As Account Manager, I want to see current facts, blockers, owners, deadlines and evidence-linked summaries, so that I can intervene without reconstructing every email.
- **Trigger and timing:** Case opened or case event committed.
- **Typical workflow:** Show structured status → refresh a source-linked narrative → show stale-summary timestamp separately.
- **Acceptance contracts:** CS-03 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-06 Extensible workflow administration

- **Scope:** Initial.
- **Story:** As Workflow administrator, I want to publish versioned client workflow packages and extend reusable steps, so that new clients and processes can be added without rewriting the core.
- **Trigger and timing:** New or revised SOP/workflow.
- **Typical workflow:** Draft configuration → validate → review → publish → pin new cases → explicitly migrate existing cases if authorized.
- **Acceptance contracts:** CFG-01, CFG-02, CFG-03 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-07 Controlled automatic responses

- **Scope:** Initial.
- **Story:** As Account Manager, I want to delegate approved low-risk acknowledgments, clarifications and reminders while reviewing sensitive responses, so that routine coordination is faster without losing authority.
- **Trigger and timing:** Validated proposal and an enabled action policy.
- **Typical workflow:** Validate facts and recipients → apply policy → review where required → authorize exact content → send → reconcile outcome.
- **Acceptance contracts:** CX-02, AU-01, AU-02, AU-03, AI-01, AI-02, AI-03, AI-04, IN-04, OP-04, QA-01, QA-02 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-08 Reliable managed operation

- **Scope:** Initial.
- **Story:** As Service owner, I want to operate, observe, recover and update AAM with managed cloud services, so that failures do not lose obligations or create uncontrolled actions.
- **Trigger and timing:** Deployment, incident, version change or operational review.
- **Typical workflow:** Monitor → diagnose → pause/repair/replay safely → verify recovery → retain attributable evidence.
- **Acceptance contracts:** AU-03, IN-04, OP-01, OP-02, OP-03, OP-04, QA-01, QA-02, WF-01, WF-02 in the [traceability index](../traceability.json); each has positive and negative scenarios.

## US-09 Quarterly consolidated reporting

- **Scope:** Extension.
- **Story:** As Regional Account Manager, I want to collect and consolidate reports and country commentary, so that regional clients receive coherent performance insights.
- **Trigger and timing:** Quarterly.
- **Typical workflow:** Resolve approved reporting-source access → collect current country reports and commentary → consolidate → approve and share.
- **Extension acceptance:** Required inputs, responsible parties, review/release and evidence-based completion must be defined in a dedicated change; missing-country data must remain explicit rather than silently omitted. Reuse the workflow primitives under CFG-02.

## US-10 Health talk tracker

- **Scope:** Extension.
- **Story:** As Account Manager, I want to collect annual local team utilisation and health-talk/event updates, so that regional clients understand program activity.
- **Trigger and timing:** Annually during the policy year.
- **Typical workflow:** Collect country updates → track gaps → consolidate → approve and share with regional client/broker.
- **Extension acceptance:** Required inputs, responsible parties, review/release and evidence-based completion must be defined in a dedicated change; missing-country data must remain explicit rather than silently omitted. Reuse the workflow primitives under CFG-02.

## US-11 Pool report issuance

- **Scope:** Extension.
- **Story:** As Regional Account Manager, I want to coordinate Reporting Team pool reports and executive summaries, so that clients receive complete pool reporting within the applicable SLA.
- **Trigger and timing:** According to the configured report period and contractual SLA.
- **Typical workflow:** Track Reporting Team report → prepare regional executive summary → approve → share client/broker.
- **Extension acceptance:** Required inputs, responsible parties, review/release and evidence-based completion must be defined in a dedicated change; missing-country data must remain explicit rather than silently omitted. Reuse the workflow primitives under CFG-02.

## US-12 Client review meetings

- **Scope:** Extension.
- **Story:** As Account Manager, I want to prepare review materials and follow meeting actions, so that program reviews lead to completed actions.
- **Trigger and timing:** Monthly, quarterly or semi-annually.
- **Typical workflow:** Prepare reporting materials → review program performance → capture owners/deadlines → follow actions.
- **Extension acceptance:** Required inputs, responsible parties, review/release and evidence-based completion must be defined in a dedicated change; missing-country data must remain explicit rather than silently omitted. Reuse the workflow primitives under CFG-02.

## US-13 SLA and service reviews

- **Scope:** Extension.
- **Story:** As Account Manager, I want to collect and consolidate country service results, so that clients can review service performance consistently.
- **Trigger and timing:** Quarterly or semi-annually.
- **Typical workflow:** Collect local team SLA results → validate coverage → consolidate → approve and share.
- **Extension acceptance:** Required inputs, responsible parties, review/release and evidence-based completion must be defined in a dedicated change; missing-country data must remain explicit rather than silently omitted. Reuse the workflow primitives under CFG-02.

## US-14 Renewal timeline planning

- **Scope:** Extension.
- **Story:** As Account Manager, I want to agree renewal milestones across underwriting and local teams, so that stakeholders work to one approved renewal timeline.
- **Trigger and timing:** Annually.
- **Typical workflow:** Draft timeline → obtain underwriting team agreement → obtain local team consensus → share with client/broker.
- **Extension acceptance:** Required inputs, responsible parties, review/release and evidence-based completion must be defined in a dedicated change; missing-country data must remain explicit rather than silently omitted. Reuse the workflow primitives under CFG-02.

## US-15 Renewal meetings

- **Scope:** Extension.
- **Story:** As Account Manager, I want to prepare consolidated renewal updates and track meeting actions, so that renewal strategy discussions lead to accountable follow-up.
- **Trigger and timing:** During renewal cycle.
- **Typical workflow:** Prepare updates/terms → review strategy with client/broker → track and follow actions.
- **Extension acceptance:** Required inputs, responsible parties, review/release and evidence-based completion must be defined in a dedicated change; missing-country data must remain explicit rather than silently omitted. Reuse the workflow primitives under CFG-02.

## Business-owner refinement

The story text preserves source intent. Exact evidence, action authority, recipient/multi-client rules, manual controls, consolidation scope and notifications remain open in [the decision register](decisions.md). Engineering acceptance fixtures do not answer those questions.

## Shared business vocabulary

AM means Account Manager; a local team is a configurable insurance-service role; UW means underwriting; SOP means Standard Operating Procedure. Authority and contact mappings are deployment-specific. A case is a business coordination record; an obligation is a specific owed deliverable/action; an agent run is a technical processing attempt. None is equivalent to an email thread.
