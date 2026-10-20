---
name: lockin
description: Lock in on a goal that can be checked and work autonomously until it's provably met or a cap is hit, looping act → prove → judge. Use when the user runs /lockin, says "lock in", "keep going until…", "don't stop until…", or hands over a task with a clear finish line (tests pass, build green, queue empty).
---

# /lockin

Lock in on one goal and keep working until it is provably true. No stopping for check-ins, no claiming done without evidence.

## 1. Lock the goal

Turn the request into a done-condition with four parts:

| Part            | Example                                                                     |
| --------------- | --------------------------------------------------------------------------- |
| **End state**   | All tests in `test/auth` pass and lint is clean                             |
| **Proof**       | `npm test test/auth` exits 0; `npm run lint` reports 0 errors               |
| **Constraints** | Don't touch the public API in `src/auth/index.ts`; don't edit or skip tests |
| **Cap**         | Stop after 25 iterations (default if the user gives none)                   |

### Examples

**Fix failing tests**

> "get the auth tests passing"

```
LOCKED IN
End state:   All tests in test/auth pass and lint is clean
Proof:       `npm test test/auth` exits 0; `npm run lint` reports 0 errors
Constraints: Don't touch the public API in src/auth/index.ts; don't edit or skip tests
Cap:         15 iterations
```

**Fix a bug**

> "checkout crashes when the cart is empty"

```
LOCKED IN
End state:   An empty cart shows "Your cart is empty" instead of throwing
Proof:       New test `checkout.empty-cart.test.ts` fails before the fix and passes after; full suite green
Constraints: No changes to payment flow files in src/payments/
Cap:         15 iterations
```

**Type errors**

> "turn on strict mode"

```
LOCKED IN
End state:   tsconfig has "strict": true and the project type-checks
Proof:       `npx tsc --noEmit` exits 0 with 0 errors
Constraints: No `any`, `@ts-ignore`, or `@ts-expect-error` added; no runtime behavior changes
Cap:         50 iterations
```

## 2. Stay locked in

Each iteration:

1. **Plan:** name the single next step that moves toward the end state.
2. **Act:** edit, run, investigate.
3. **Prove:** run the proof commands. Paste the real output (exit codes, test summary, error counts) into the transcript. Unshown evidence doesn't count.
4. **Judge:** one line: `[n/cap] NOT MET — <reason>` or `[n/cap] MET — <evidence>`.

Rules:

- **Don't break focus to ask** unless you're blocked on a decision that can't be undone, or on missing access or info the codebase can't supply.
- **Never weaken the goal.** Deleting, skipping or loosening tests or checks to make them pass is a failure, not progress.
- **If the same failure shows up 3 iterations in a row,** change approach. Re-read the error, question your assumption, look wider in the codebase.
- **Respect the constraints.** Run `git diff` before judging MET to confirm nothing out of scope changed.

## 3. Verify independently

Before declaring MET, if subagents are available, spawn a fresh one that hasn't seen your work. Give it only the `LOCKED IN` block, then have it re-run the proof commands and check the diff against the constraints. If it disagrees, go back to the loop with its reason.

## 4. Unlock

- **MET:** report the goal, the final proof output, files changed, and the iteration count.
- **Cap reached or blocked:** report what's done, what's still failing (with output), what's blocking, and a suggested next `/lockin` goal.

Never unlock on MET without proof from the final iteration.
