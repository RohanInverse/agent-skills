---
name: ultra-engineering
description: Orchestrate prompt-driven repository coding and code review with parallel investigation, one write-owning maker, independent read-only checking, iterative correction, and fresh verification. Use for implementation, debugging, refactoring, test or build repair, diagnosis, review, audit, and mixed review-and-fix requests; infer edit authority and scope from the user's prompt. Do not use for non-coding work.
---

# Ultra Engineering

Drive the user's repository task to an evidence-backed outcome. The user prompt
defines both the work and whether workspace changes are authorized; "ultra"
increases depth and independence, not scope.

## Choose the Mode From the Prompt

- **Review, audit, diagnose, or explain:** remain read-only. Inspect and report;
  do not implement fixes unless the prompt also requests changes.
- **Build, fix, implement, refactor, or repair:** edit the repository, verify the
  result, then obtain an independent read-only review of the complete task diff.
- **Review and fix:** follow any order the user specifies. Otherwise review the
  stable baseline first, give only accepted in-scope findings to the writer, then
  independently review the complete resulting diff.

Honor any explicit files, commits, branch, base, constraints, checks, and output
format. Never reuse a range or assumption from an earlier task merely because it
was convenient there. If a branch or commit review base is omitted, infer the most
defensible base from upstream and merge-base information and state the assumption.
Ask only when a missing product decision would materially change the outcome.

If the wording is ambiguous about whether edits are authorized, choose the
narrowest read-only interpretation or ask before writing.

## Establish the Contract

Before delegating or editing:

1. Read applicable repository instructions and inspect the worktree, relevant
   history, code, tests, and build configuration. When Git is available, record
   `HEAD` and distinguish staged, unstaged, and untracked state before edits.
2. Translate the prompt into observable completion criteria and a bounded scope.
3. Record pre-existing or unrelated changes and preserve them.
4. Identify the highest-risk behavior and the commands or reproductions that can
   verify it.
5. Make reasonable, reversible assumptions when they stay inside the requested
   scope; surface consequential assumptions to the user.

Do not commit, push, open a pull request, deploy, change external systems, or
perform destructive cleanup unless the user explicitly authorizes that action.

## Use Parallelism Without Losing Independence

For non-trivial work, use available subagents for concrete, bounded surfaces that
can be inspected in parallel. Split by real boundaries such as backend, frontend,
data, security, or a distinct feature—not arbitrary file counts.

- Give every agent the original user request, exact scope, repository location,
  relevant constraints, and whether it is read-only.
- For review-only tasks, every subagent is read-only.
- For change tasks, keep one workspace writer at a time; discovery and review
  agents remain read-only. Do not let agents race over shared files.
- Keep at least one reviewer independent from implementation. Give that reviewer
  the requirements, complete task diff, relevant surrounding code, and raw
  verification output—not the implementer's conclusions.
- Treat agent output as leads. The orchestrator must inspect the cited code and
  evidence before accepting, rejecting, or reporting a finding.
- Resolve disagreements with reproducible behavior, contracts, tests, or source
  code; never by majority vote.
- Review a stable task snapshot. Any mutation that touches the reviewed scope or
  its behavior invalidates the verdict and requires review of the complete
  updated task diff from the original baseline.

The complete task diff means every in-scope change relative to the recorded
baseline, plus relevant overlapping context. Exclude unrelated dirty changes from
the review input while still inspecting global status for accidental edits.

Do not create busywork for a trivial change. If genuine reviewer capacity is
temporarily busy, wait while useful local work remains. If independent review is
unavailable, do not simulate it or claim it occurred; report the missing gate.

## Change Workflow

1. Trace the affected behavior through its callers, data contracts, and tests.
2. Implement the smallest coherent root-cause change that satisfies the prompt.
   Reuse repository patterns and installed capabilities; avoid speculative
   abstractions, broad rewrites, and unrelated cleanup.
3. Add or update the smallest meaningful regression test when behavior changed.
   Do not weaken tests, disable checks, swallow errors, or hard-code outputs to
   manufacture a pass.
4. Run focused verification first, then broader checks proportionate to risk.
5. Inspect the actual diff and verification output yourself.
6. Send the complete task diff and evidence to the independent reviewer.
7. Reproduce and triage every actionable finding. Return valid findings to the
   implementer, rerun the affected checks, and ask the same reviewer to assess the
   complete updated task diff.
8. Repeat until no actionable finding remains and every applicable completion
   criterion has evidence, or a genuine blocker is reached.

## Review Workflow

1. Resolve the exact review range or artifact and inspect every changed file. For
   branch or commit reviews, pin and report base/target SHAs and use the branch
   merge base or intended commit parent. For a working tree, include staged,
   unstaged, and relevant untracked content. Do not mix unrelated dirty changes
   into a commit-range review. Without Git, use the user-supplied artifact and
   state which provenance checks are unavailable.
2. Read enough surrounding code, callers, types, tests, migrations, and history to
   judge behavior rather than isolated lines.
3. Run relevant static checks, tests, builds, and focused reproductions when safe.
4. Require at least one independent read-only review pass. Add parallel specialist
   passes when the scope materially benefits from separate lenses.
5. Recheck every candidate against the actual task diff. For a diff review, report a
   defect only when the reviewed change introduced it or made it newly reachable;
   keep unrelated pre-existing defects out unless the user requested an audit.
6. Lead with verified findings ordered by severity. If none remain, state that and
   list residual verification gaps.

Apply only the lenses relevant to the change, with special attention to:

- requirements and edge cases;
- call-site and API/data-contract compatibility;
- loading, empty, error, cached, retry, pagination, and lifecycle states;
- authorization, tenant isolation, validation, secrets, and data loss;
- concurrency, transactions, time boundaries, and backward compatibility;
- UI behavior, keyboard access, and accessibility basics;
- tests that can pass while the real path is still broken.

Reject style-only comments, unproven speculation, and requests for abstractions
without a demonstrated need.

## Finding Standard

Each finding must include:

- `P0`, `P1`, `P2`, or `P3` priority;
- the narrowest useful file and line;
- the triggering conditions and user-visible or operational impact;
- concrete evidence from code, a reproduction, or command output;
- the required direction of the fix.

Do not report generated output, an agent's assertion, or a passing unrelated test
as proof. Combine findings only when they share one root cause and one fix.

## Verification and Completion

Before any success claim, run the proving commands fresh and read their complete
relevant output and exit codes. Prefer, as applicable:

- a reproduction of the original failure and its regression test;
- focused tests for the changed behavior;
- type checks, lint, broader test suites, and a production build;
- migration or compatibility checks;
- final `git diff`, `git diff --check`, and `git status` inspection.

Confirm that tests actually ran, including pass/fail counts and unexpected skips.
Treat formatters, snapshot updates, generators, builds, and dependency commands as
possible mutations: inspect their resulting changes and rerun affected checks.

Classify failures as introduced, pre-existing, flaky, or environmental only when
evidence supports the classification. Never hide a red check behind a successful
partial check. If verification cannot run, state exactly what remains unverified.
When a command is missing from `PATH`, inspect repository toolchain configuration,
version managers, and known local installations before declaring it unavailable;
do not install or upgrade tooling without authorization.

For a change task, finish only when the requested behavior is evidenced, the
independent reviewer has no actionable finding, relevant checks pass or are
explicitly accounted for, and the final diff contains no accidental changes. For
a review task, finish only after reconciling the independent passes and validating
the reported findings against the requested scope.

Keep verdicts separate: reviewer approval means no actionable code finding remains;
it does not prove that tests, builds, or runtime checks passed.

Report the outcome first, followed by the most important changes or findings,
exact verification commands and results, and any remaining limitation. Keep
progress updates concise while the work is running.
