---
name: pre-flight
description: Pre-production code review against a user-chosen healthy point. Use when asked to review a branch before merge, whether it's safe to ship, or what blocks a PR.
disable-model-invocation: true

parameters:
  healthy_point:
    type: string
    required: false
    default: main
    description: A Git commit SHA, branch, tag, or ref identifying a known healthy state of the application.
---

# Pre-production code review

Find every reason this branch should NOT ship. No verdict until each one is resolved.

## Phase 1: Pin the healthy point

The `healthy_point` parameter identifies the known healthy state to compare against. It can be a commit SHA, branch, tag, `main`, `HEAD~5`, or any other valid Git ref.

Before going further:

1. Confirm it resolves: `git rev-parse {{healthy_point}}`
2. Write the diff command once and reuse it everywhere: `git diff {{healthy_point}}...HEAD` Three dots, so Git compares against the merge-base.
3. Confirm the diff is non-empty. A bad ref or empty diff should fail here, not mid-review.

## Phase 2: Read the diff and its context

1. Run the captured diff command for the full changeset.
2. For every changed file, read enough surrounding code to understand the original contract and what changed.
3. Check related code for auth, data models, API contracts, config, migrations, shared utilities, error handling, and logging.
4. Answer your own questions from the codebase. Ask the user only what the code can't tell you.

## Phase 3: Classify the risks

Classify each change, ordered by severity:

| Label         | Meaning                                                 |
| ------------- | ------------------------------------------------------- |
| 🔴 Breaking   | Changes observable behavior or an API contract          |
| 🟠 Conflict   | Contradicts the system spec or codebase conventions     |
| 🟡 Incomplete | Happy path only, TODOs, unhandled errors, dead branches |
| 🟢 Safe       | No effect outside its module                            |

Post the findings list before moving on: label, `file:line`, one-line reason. Summarize 🟢 changes as a count, not a list.

## Phase 4: Adversarial Interview

For every non-🟢 finding and every implicit decision behind it:

- Ask one question at a time, with your recommended answer. Wait for the reply.
- Resolve dependent questions in order.
- Continue until no implicit assumptions remain.

A finding is resolved only when it is either:

- **Fixed** — you re-read the code and confirmed the fix is on the branch, or
- **Accepted** — the user explicitly confirmed it is intended, with a reason.

## Phase 5: Decide whether it ships (Go / No-Go)

Only once every finding is resolved.

- ✅ **SHIP** — every finding is 🟢, fixed, or accepted. List accepted risks in one line each.
- 🚫 **DO NOT SHIP** — numbered list of each blocking action, with `file:line` and the relevant snippet.
