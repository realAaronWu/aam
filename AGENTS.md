# AAM working agreements

## Scope

- Read the README, relevant OpenSpec change, story catalogue and testing strategy before changing behavior.
- Planning tasks do not authorize implementation, deployment or external email.
- Preserve stable story, requirement, scenario and task IDs; update traceability with scope changes.
- Models propose; application code controls context, authorization, delivery and business completion.
- Keep this repository generic. Use synthetic examples and do not commit company-specific source material, private correspondence, credentials, local artifacts or production data.

## Engineering quality

1. Design critical contracts before coding: map requirements to implementation, positive/negative examples and verification evidence; specify failure and compatibility boundaries.
2. Test complete acceptance paths, including final status, reports and diagnostics. Missing evidence is not success.
3. Challenge checkers with incomplete output, crashes containing expected text, wrong statuses/types, partial timeouts and input/oracle drift. Use targeted disposable mutations for consequential guards.
4. Enforce shared invariants across normal, fallback, attached, smoke and repair paths; separate structured status from diagnostic text and distinguish local failure, setup failure and interrupt.
5. Review delivery as code: qualified retrievable dependencies, persisted metadata, complete inventory, deterministic generated metadata and tracked instructions; reproduce relevant provisioning from clean checkout and cold cache.
6. Verify actual CI execution: discovery, events, matrices, dependencies, concurrency, artifact provenance and failure propagation. Retain failed logs and routine cheap negative tests.
7. Complete independent correctness and delivery review of the actual revision and record findings/closure. A request, assignment, silence or self-review is not completed independent review. Mark unavailable gates pending.
8. After false acceptance or a missed invariant, audit neighboring paths and the failure class, add regression coverage and demonstrate closure.
9. Audit the exact submission against the target branch and promised file list; qualify changed dependencies and rerun affected checks with source/artifact provenance.
10. Report PASS / FAIL / NOT RUN honestly. Counts are not proof; separate fixtures, live models, provider integration and production qualification.

Before opening a PR, finish applicable local gates and independent review. If CI needs publication, use a clearly identified validation draft listing pending evidence. Close internal findings before upstream review or ready status. These gates require autonomous validation within existing authorization, not repeated permission questions.
