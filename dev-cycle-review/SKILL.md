---
name: dev-cycle-review
description: "[Step 6/7 — Core Cycle] Sixth step of the development cycle. Use this skill after cleanup is complete and tests are passing. Triggers when the user says things like "let's review", "review the changes", "look for anything missed", "sanity check", or transitions from completed cleanup. Requires dev-cycle-cleanup to have been completed. This step combines Claude's analysis with the user's own review before pushing to the repository.
---

# Development Cycle: Review

This is **Step 6** of the development cycle. This is a collaborative checkpoint before anything is pushed. The goal is to catch anything missed during implementation, documentation, or cleanup — before it becomes permanent in the repository.

## Purpose

No matter how disciplined the cycle, things get missed. Review is the last line of defense before the code leaves the local environment. It combines Claude's systematic analysis with your own judgment and intuition — both are needed.

## Prerequisites

- `dev-cycle-cleanup` must be complete
- All tests must be passing
- Linting must be clean
- Session plan file at `./docs/[task-name].md` should be in context

## Claude's Review

Work through the changed files systematically. For each file changed during this session:

### Code Review
- Does the implementation satisfy each acceptance criterion listed in the session plan? (Check off each one explicitly if criteria were written.)
- Does the implementation match the intent stated in the session plan?
- Are there any edge cases not covered by the current tests?
- Are there any error handling gaps — uncaught exceptions, unhandled promise rejections, missing null checks?
- Is there any logic that works for the happy path but breaks under unexpected input?
- Are there any hardcoded values that should be constants or config?
- Are there any console.log or debug statements left in the code?

### Test Review
- Do the tests actually test the right things, or do they just pass?
- Are there any obvious missing test cases for the new or changed functionality?
- Are any tests too tightly coupled to implementation details (brittle tests)?

### Documentation Review
- Are comments accurate to what the code actually does?
- Are timestamps and author attribution present on all changed functions?
- Are any TODO comments actionable and clear?

### Hot Path Review
- Were any hot path candidates touched during implementation?
- Are they flagged with comments for runtime profiling?
- Was anything inadvertently added that could become a performance issue (new loop over large data, synchronous blocking call, etc.)?

## Presenting Findings

After reviewing all changed files, present findings grouped by severity:

**Must fix before push** — bugs, missing error handling, broken logic, debug statements left in code
**Should discuss** — potential edge cases, brittle tests, unclear comments, hot path concerns
**Minor / optional** — style preferences, small improvements that don't affect correctness

For each finding, provide:
- Where it is (file and line)
- What the concern is
- Your recommendation

Do not fix anything during the review presentation. Wait for user direction.

## User's Review

After presenting Claude's findings, prompt the user to do their own pass:

- Ask if anything felt uncertain or improvised during implementation that should be revisited
- Ask if the implementation matches their mental model of how the feature should work
- Ask if there is anything the automated tests cannot catch that they want to manually verify

## Resolving Findings

For each finding the user agrees should be addressed:
- Treat it as a mini implementation cycle — make the change, re-run tests, confirm clean
- Log any findings the user decides to defer in the **Out of Scope** section of the session plan

## Hard Rules

- **Do not push to the repository until review is complete and the user confirms they are satisfied**
- **Do not skip findings** to move faster — present everything and let the user triage
- **Do not make changes during the review presentation** — changes happen after the user has seen all findings
- **Do not close out the review** if must-fix items are unresolved

## Next Step

Once Claude's review is complete, user review is complete, and all must-fix items are resolved: proceed to **dev-cycle-git**.
