---
name: run-code-review-loop
description: Coordinate a two-agent coding and review loop in which one agent implements and tests a requested change while a separate read-only agent independently reviews the requirements, diff, and verification evidence; repeat implementation and review until checks pass and no actionable findings remain. Use for feature work, bug fixes, refactors, test repairs, and other repository changes where the user requests independent code review, maker-checker separation, or one agent to code and another to review.
---

# Run Code Review Loop

Coordinate two long-lived subagents with distinct responsibilities. Keep the implementer responsible for code changes and the reviewer independent and read-only.

## Establish the Contract

Before delegating:

1. Convert the request into observable completion criteria.
2. Inspect repository instructions, current status, relevant code, and available checks sufficiently to define safe agent scopes.
3. Record pre-existing or unrelated changes that both agents must preserve.
4. Ask the user only when a missing decision would materially alter the solution or additional authority is required.

## Assign the Agents

Spawn exactly these roles when agent capacity is available:

### Implementer

Give the implementer the request, completion criteria, repository location, constraints, and known pre-existing changes. Instruct it to:

- inspect before editing;
- implement the smallest coherent solution;
- add or update meaningful tests when appropriate;
- run focused checks, then broader checks justified by risk;
- preserve unrelated work;
- return the changed files, commands run, results, and any uncertainty.

The implementer owns all code and test edits throughout the loop.

### Reviewer

Give the reviewer the original request and completion criteria. After implementation, give it the actual final diff, relevant files, and raw verification results. Do not give it the implementer's private reasoning, intended answer, or conclusions. Instruct it to:

- remain read-only and make no workspace edits;
- independently inspect the affected code and relevant surrounding behavior;
- look for correctness defects, missed requirements, regressions, unsafe behavior, security issues, concurrency or state problems, error-handling gaps, compatibility problems, and inadequate tests;
- avoid style-only findings unless repository rules require them or readability creates material risk;
- cite each finding with location, impact, evidence, and a concrete required change;
- label findings as `P0`, `P1`, `P2`, or `P3`;
- respond `APPROVED` only when no actionable finding remains.

Do not ask the reviewer to implement its own recommendations.

## Run the Loop

Use this sequence:

1. Send the task to the implementer and wait for its implementation and test evidence.
2. Inspect the workspace and collect the actual diff and relevant test output. Do not rely only on the implementer's summary.
3. Send the same reviewer the original requirements plus the current full diff and evidence.
4. Triage every finding:
   - Return valid actionable findings to the implementer.
   - Return ambiguous findings for investigation and evidence.
   - Reject incorrect or out-of-scope findings only with a concrete explanation.
5. Have the implementer address accepted findings and rerun the appropriate checks.
6. Send the reviewer the complete updated diff, not only the latest patch, for another independent pass.
7. Repeat until the reviewer returns `APPROVED` and all applicable completion gates pass.

Keep both roles stable across iterations so responsibility and review continuity remain clear. Never let the implementer approve its own work.

## Resolve Disagreement

When the implementer disputes a finding:

1. Require evidence such as code paths, tests, specifications, or reproducible behavior.
2. Give the reviewer the evidence and request a revised judgment.
3. Accept a withdrawal when the evidence resolves the issue.
4. If a material disagreement remains and the correct behavior depends on a product decision, stop and ask the user for the smallest necessary decision.

Do not resolve disputes by majority vote or by silently ignoring a finding.

## Completion Gates

Finish successfully only when:

- the requested behavior satisfies the completion criteria;
- relevant focused checks pass;
- broader checks appropriate to the risk pass or any unavailable check is clearly accounted for;
- the reviewer returns `APPROVED` against the complete current diff;
- the final diff contains no accidental or unrelated changes;
- no known task-specific regression or unresolved finding remains.

Do not treat `APPROVED` as a substitute for executable verification, and do not treat passing tests as a substitute for independent review.

## Stop Safely

Stop as blocked when progress requires unavailable credentials, permissions, external coordination, inaccessible dependencies without a safe substitute, or a material product decision. Also stop when repeated rounds produce no new evidence or alternate between the same positions.

If two subagent slots are unavailable, do not simulate independence by performing both roles in one context. Explain that a genuine two-agent review could not be completed and ask whether to proceed with a single-agent fallback.

## Report the Outcome

Lead with the result and summarize:

- what the implementer changed;
- what the reviewer found and what was corrected;
- the final review status;
- the checks run and their outcomes;
- any remaining limitation, pre-existing failure, or verification gap.

Do not expose private chain-of-thought or internal deliberations from either agent.
