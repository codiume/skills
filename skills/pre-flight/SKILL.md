---
name: pre-flight
description: Pre-production code review against a user-chosen healthy point. Use whenever a user wants to review a branch before shipping, asks if code is ready to merge, wants blockers identified before a PR lands.
disable-model-invocation: true
---

# Pre-production code review

Look for reasons this branch should not ship. Verify each finding before giving a verdict.

## Phase 1: Pin the healthy point

The user gives you the healthy point: a commit `SHA`, `branch`, `tag`, `main`, `HEAD~5`, anything naming a moment when the app worked (production). If they didn't specify one, use `main`.

Before going further:

1. Confirm it resolves: `git rev-parse <healthy-point>`.
2. Write the diff command once and reuse it everywhere: `git diff <healthy-point>...HEAD`. Three dots, so git compares against the merge-base.
3. Confirm the diff is non-empty. A bad ref or empty diff should fail here, not mid-review.

## Phase 2: Read the diff and its context

1. Run the captured diff command for the full changeset.
2. For every changed file, read enough surrounding code to understand the original contract and what changed.
3. Check related code for auth, data models, API contracts, config, migrations, shared utilities, error handling, and logging.
4. If something is unclear, inspect the codebase before asking a question. Ask only when the code cannot answer it.

## Phase 3: Classify the risks

Give every change one label:

| Label         | Meaning                                                      |
| ------------- | ------------------------------------------------------------ |
| 🔴 Breaking   | Changes observable behavior or an API contract               |
| 🟡 Incomplete | Covers only the happy path, or leaves TODOs or dead branches |
| 🟠 Conflict   | Contradicts the system spec or project conventions           |
| 🟢 Safe       | Has no side effects outside its module                       |

## Phase 4: Adversarial Interview

Work through every implicit decision, unclear behavior, and non-obvious tradeoff in the diff.

- One question at a time; wait for a response before continuing.
- Include your recommended answer with each question.
- Resolve dependencies in order.
- Check the codebase before asking. Only raise questions the code cannot answer.
- Stop when no implicit assumptions remain.

## Phase 5: Decide whether it ships (Go / No-Go)

_Only after all Phase 4 questions are resolved._

- ✅ **SHIP**. Every risk is safe 🟢, or every finding has a confirmed fix in this branch.
- ❌ **DO NOT SHIP**. List every blocking action. Include the file path, line number, and relevant snippet for each item.
