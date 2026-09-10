---
name: verification-before-completion
description: Requires fresh verification evidence before claiming work is complete, fixed, passing, ready to commit, or ready for a pull request. Use before completion claims, commits, PR creation, status updates that imply success, or any positive assertion about tests, builds, linting, fixes, requirements, or delegated work.
---

# Verification Before Completion

## Overview

Use this skill before making any success claim about engineering work.

**Core principle:** Evidence before claims, always.

## Required Workflow

Before claiming success:

1. Identify the command or check that proves the claim.
2. Run the full verification command fresh in the current turn.
3. Read the output and exit code.
4. Confirm the output directly supports the claim.
5. Report the result with the evidence, or state the actual remaining issue.

If verification cannot be run, say so clearly and do not claim success.

## Common Failures

| Claim | Requires | Not Sufficient |
|-------|----------|----------------|
| Tests pass | Test command output: 0 failures | Previous run, "should pass" |
| Linter clean | Linter output: 0 errors | Partial check, extrapolation |
| Build succeeds | Build command: exit 0 | Linter passing, logs look good |
| Bug fixed | Test original symptom: passes | Code changed, assumed fixed |
| Regression test works | Red-green cycle verified | Test passes once |
| Requirements met | Line-by-line checklist | Tests passing |
| Ready to commit or PR | Diff reviewed and relevant checks pass | Local inspection only |

## Red Flags

- Using "should", "probably", "seems to"
- Saying "done", "fixed", "complete", "ready", or "passes" before verification
- Committing or creating a PR without fresh evidence
- Relying on partial verification
- Treating generated output, agent reports, or code review as proof

## Verification Patterns

- Tests: run the relevant test command and confirm the pass/fail count.
- Build: run the build command and confirm exit code 0.
- Linting: run the linter and confirm there are no errors.
- Bug fix: exercise the original failure path or run a regression test.
- Requirements: re-read the requested scope and verify each required item.
- Commit or PR: inspect the diff and run the appropriate checks first.
- Delegated work: verify the actual changes and outputs before summarizing them as successful.

## Reporting Format

When verification succeeds:

```markdown
Verified with `[command]`: [key result, such as exit 0 or pass count].
```

When verification is unavailable or incomplete:

```markdown
I could not verify `[claim]` because `[reason]`. I checked `[partial evidence]`, but `[remaining risk]` is still unverified.
```

## When To Apply

Apply this skill before:

- Any completion or success claim
- Any claim that tests, linting, builds, or other verification checks pass
- Committing or creating a pull request
- Saying a bug is fixed
- Reporting that requirements are met
- Summarizing delegated work as successful
