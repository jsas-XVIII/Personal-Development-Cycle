---
name: dev-cycle-implementation
description: "[Step 3/7 — Core Cycle] Third step of the development cycle. Use this skill when the user is ready to write code based on a confirmed plan. Triggers when the user says things like "let's implement", "start coding", "begin phase 1", "write the code", or confirms a plan and is ready to proceed. Requires dev-cycle-planning to have been completed and a session plan file to exist in ./docs/. This skill covers code changes and the full test loop.
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
3. **Run existing tests** — before writing any new tests, run the full test suite to surface any regressions or conflicts with existing behavior:
   - If all existing tests pass: proceed to step 4
   - If existing tests fail: work through the **Existing Test Failure** decision below before continuing
4. **Write or update tests** — as noted in the session plan for this phase:
   - New functionality requires new tests
   - Changed functionality requires updated tests
   - Do not delete tests unless the feature they cover is explicitly being removed
5. **Run the full test suite** — execute all tests after writing new or updated tests
6. **Diagnose results** — based on test output:

### Existing Test Failure

When an existing test fails after implementing phase changes, determine which of these two cases applies — they have different fixes:

**The feature intentionally changed the behavior this test was checking:**
- The test is now testing something the feature no longer does, or does differently by design
- Present this clearly to the user: what the test expects, what the feature now does, and why they differ
- Ask the user to confirm this is an intentional behavioral change before touching the test
- If confirmed: update the test to match the new behavior with a clear explanation of what changed and why
- Do not update the test based on your own judgment — always get explicit confirmation first

**The implementation accidentally broke something it should not have:**
- The test is correct and the code change introduced an unintended regression
- Fix the code, not the test
- Do not proceed until the regression is resolved

**If it is not clear which case applies:**
- Do not guess and do not change anything yet
- Present your assessment to the user:
  - What the test expects
  - What the code now does
  - Whether the behavioral difference looks intentional or accidental
  - Your recommendation and why
- Wait for the user to confirm before making any change

### Test Loop

After running the full test suite (step 5 above), apply this decision logic:

**If all tests pass:**
- Confirm phase is complete
- Update the session plan file to mark the phase done
- Move to the next phase or notify the user that all phases are complete

**If tests fail:**
- Apply the same decision logic as Existing Test Failure above — is this a regression or an intentional behavioral change?
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

## Completion Checkpoint

When all phases are complete and all tests pass, **stop here**. Do not proceed to documentation automatically.

Present a completion summary to the user:
- Files created or modified
- Test results (count passing)
- Typecheck and lint status
- Any out-of-scope items logged during implementation

Then explicitly ask the user to:
1. Review the code changes
2. Test the feature in the browser or runtime environment
3. Confirm they are satisfied before continuing

**Do not proceed to documentation until the user gives explicit approval.** Catching issues here avoids re-running documentation, cleanup, and review unnecessarily.

## Next Step

Once the user has reviewed, tested, and explicitly approved: proceed to **dev-cycle-documentation**.
