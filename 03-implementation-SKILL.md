---
name: dev-cycle-implementation
description: Third step of the development cycle. Use this skill when the user is ready to write code based on a confirmed plan. Triggers when the user says things like "let's implement", "start coding", "begin phase 1", "write the code", or confirms a plan and is ready to proceed. Requires dev-cycle-planning to have been completed and a session plan file to exist in ./docs/. This skill covers code changes and the full test loop.
---

# Development Cycle: Implementation

This is **Step 3** of the development cycle. This is where code gets written, tests are run, and the implementation is validated against the plan.

## Purpose

Execute the confirmed plan phase by phase, maintaining quality and test integrity throughout. The goal is working, tested code — not just code that appears to work.

## Prerequisites

- `dev-cycle-planning` must have been completed
- Session plan file must exist in `./docs/[task-name].md`
- `ROADMAP.md` must be readable in project root
- If either is missing, stop and ask the user to complete the planning step first

## How to Proceed

### Phase Execution

Work through phases in the order defined in the session plan. Do not skip ahead or combine phases without checking with the user first.

For each phase:

1. **State the phase** — announce which phase you are starting and what it covers
2. **Implement the changes** — write the code for that phase only
3. **Write or update tests** — as noted in the session plan for this phase:
   - New functionality requires new tests
   - Changed functionality requires updated tests
   - Do not delete tests unless the feature they cover is explicitly being removed
4. **Run the tests** — execute the test suite after each phase
5. **Diagnose results** — based on test output:

### Test Loop

After running tests, apply this decision logic:

**If tests pass:**
- Confirm phase is complete
- Update the session plan file to mark the phase done
- Move to the next phase or notify the user that all phases are complete

**If tests fail:**
- Determine whether the failure is in the code or the test:
  - *Code is wrong* — the implementation does not match the intended behavior. Fix the code, not the test.
  - *Test is wrong* — the test was written incorrectly, tests the wrong thing, or the expected behavior legitimately changed as part of this task. Fix the test with a clear explanation of why.
  - *Unclear* — do not guess and do not change anything yet. Present your best assessment to the user:
    - What the test expects
    - What the code actually does
    - Your best hypothesis for why they disagree (code bug, test assumption, or behavior change)
    - Your recommendation: which you think should change and why
    - Ask the user to confirm before proceeding. If the user disagrees, follow their direction.
- Re-run tests after any fix
- Do not move to the next phase until the current phase passes

### Scope Discipline

- Implement only what is in the session plan for the current phase
- If a better approach becomes apparent during implementation, stop and discuss with the user — do not silently change the plan
- If something out of scope is discovered (a bug, a missing feature, a fragile pattern), add it to the **Out of Scope** section of the session plan file — do not fix it now
- If a hot path candidate from analysis is touched during implementation, add a comment flagging it for runtime profiling — do not optimize it without explicit instruction

## Abort Protocol

A normal test failure or out-of-scope discovery is handled within the cycle. An abort is different — it is triggered when continuing implementation would be wrong, not just difficult.

**Trigger an abort when:**
- A discovered constraint makes the planned approach unworkable (wrong abstraction, missing dependency, architectural conflict)
- Completing the plan would require rewriting phases that already passed — the plan is structurally broken, not just a phase behind
- The goal itself turns out to be different from what was understood at planning time

**Do not trigger an abort for:** failing tests, scope discoveries, or a better approach emerging mid-phase. Those are handled by the test loop and scope discipline sections above.

---

When an abort is warranted, stop immediately and do not write any more code. Then work through these steps in order:

### 1. State the reason clearly
Explain to the user in plain terms:
- What was discovered
- Why it breaks the plan (not just a phase — the approach itself)
- What continuing would cost (rework, wrong direction, compounding bad assumptions)

### 2. Assess the code state
Run `git status` and `git diff` to show the user exactly what has changed since the session started.

Ask the user to choose one of:
- **Stash** (`git stash`) — preserves the work in case it has reference value or is partially salvageable
- **Revert** (`git checkout .` + remove any new untracked files) — returns to a clean state immediately
- **Keep as-is** — only if the user explicitly wants to inspect or manually recover something

Do not choose for them. The work may have diagnostic value even if the approach was wrong.

### 3. Update the session plan
Do not delete `./docs/[task-name].md`. Add an abort section at the top:

```markdown
## Abort
Date: [date]
Reason: [what was discovered and why the plan could not continue]
Code state: [stashed / reverted / kept]
```

This preserves the context for the next attempt.

### 4. Determine re-entry point
Ask the user which step to return to:
- **Back to planning (02)** — if the goal is still valid but the approach needs to change. Reference the aborted session plan in the new planning session so the constraint is not forgotten.
- **Back to analysis (01)** — if what was discovered changes the understanding of the codebase enough that planning would be premature.

Do not restart implementation without going through planning again. The plan that caused the abort cannot be resumed.

---

**Hard rules for abort:**
- **Never leave uncommitted changes without an explicit stash or revert decision**
- **Never delete the session plan on abort** — it carries the reason and context for the next attempt
- **Never re-enter implementation directly** — always go through planning first

## Comments During Implementation

Do not add comments during this step. Comments are added in the documentation step after implementation is stable. Adding comments during active coding creates noise and risks comments becoming stale if the code changes.

## Hard Rules

- **Follow the session plan** — do not deviate without user confirmation
- **Never delete a passing test** unless the feature it covers is explicitly removed
- **Never fix a test just to make it pass** without understanding why it failed
- **Do not optimize** during implementation unless it is explicitly part of the plan
- **Do not proceed to the next phase** if the current phase has failing tests
- **Do not add comments** — that is the next step

## Session Plan Updates

During implementation, keep `./docs/[task-name].md` current:
- Mark each phase complete as it finishes
- Log any out-of-scope discoveries under **Out of Scope**
- Note any hot path candidates touched during implementation

## Next Step

Once all phases are complete and all tests pass: proceed to **dev-cycle-documentation**.
