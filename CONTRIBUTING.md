# Specification driven development workflow

## Sources of truth

| Concern | Authoritative location |
| --- | --- |
| Business intent and source provenance | `docs/product/user-stories.md` and `docs/product/sources.md` |
| Proposed observable behavior | Active change `specs/<capability>/spec.md` |
| Implemented behavior after archive | `openspec/specs/<capability>/spec.md` |
| Architecture and rationale | Owning change `design.md`; shared contracts in `docs/architecture/` |
| Implementation completion | Owning change `tasks.md` with evidence |
| Cross references | `docs/traceability.json`, a review index rather than a second copy of requirement text |
| Verification policy | `docs/testing/strategy.md` |

## Add or revise a user story

1. Add the next stable US ID, actor, need, value, source, trigger, scope and acceptance links. Do not renumber existing IDs. Identify whether it is source-derived or a new business request.
2. Run `npx --no-install openspec new change <feature-name>` and inspect `openspec instructions proposal --change <feature-name>`. Declare affected capabilities.
3. Follow the CLI artifact instructions: write observable requirements before architecture and tasks. Every normative requirement uses SHALL/MUST and at least one positive and one adverse GIVEN/WHEN/THEN scenario.
4. Add new requirement IDs within the relevant capability prefix. For modified behavior, copy the complete existing requirement into a MODIFIED Requirements delta, preserving its ID. Do not create competing deltas in multiple active changes.
5. Update design decisions, typed contracts, task dependencies, traceability and test expectations together. Keep unresolved business questions in the decision register with owner and activation block; do not invent business rules. Preserve stable IDs. Assign each scenario exactly one closing task and distinguish partial contributions; mirror milestone, dependencies and ownership in traceability.json.
6. Validate with `npm run spec:validate`. Review for business completeness as well as syntax. Passing the CLI validator does not prove implementation or semantic completeness.

Templates in [docs/templates](docs/templates/) provide optional story and task/acceptance checklists. OpenSpec's actual artifact templates and schema remain authoritative for file formats.

## Implement and verify

Implementation starts only when requested. Work through a bounded task and its dependencies, retaining requirement IDs in tests and evidence. Do not mark a checkbox complete on helper tests alone when the acceptance boundary is a full orchestration or external action. Record PASS / FAIL / NOT RUN and distinguish fixtures from live integrations.

Obtain independent correctness and delivery review before PR/production readiness, and identify the reviewed revision. If real CI requires publication, use an explicitly labeled validation draft with pending evidence. No existing authorization needs to be asked for again merely to perform these checks.

## Promote specifications

Use the current OpenSpec apply/sync/archive CLI instructions. Archive only completed and validated scope, not merely a finished plan. For phased delivery, split the initial large change into non-overlapping milestone changes while preserving IDs and traceability. Confirm that merged main specs describe what is actually implemented. Never manually mark future work as shipped to make status green.

## Review ergonomics

Keep one capability per spec file; use relative repository links; keep examples synthetic; keep confidential source material outside the repository. Put detailed implementation choices in design, business contracts in specs, and actual execution evidence in revision-linked reports. A new feature should require adding or modifying its own files, not rewriting one monolithic specification.
