---
name: dev-cycle-refactor-bug-hunting
description: "[Refactor 4/5 — light path for Fibonacci 1/2/3, full 7-step cycle for Fibonacci 5+] Refactor variant of the development cycle focused on finding bugs, logic errors, edge cases, and fragile code. Use this skill when the user wants to proactively hunt for bugs rather than react to them. Triggers when the user says things like "bug hunting", "look for bugs", "find edge cases", "fragile code review", "logic errors", "let's find what's broken", or initiates a refactor session focused on correctness and reliability. Always runs through the full development cycle. Do not fix anything during analysis — produce findings first.
---

# Development Cycle: Refactor — Bug Hunting

This is a **refactor variant** of the development cycle focused on proactively finding bugs, logic errors, edge cases, and fragile patterns before they surface in production. The goal is a prioritized findings report followed by targeted fixes with verified test coverage.

## Model Recommendation

> **Consider switching to Opus for the analysis phase of this skill.** Bug hunting requires tracing logic across multiple files, reasoning about how data flows through the system, and identifying non-obvious failure conditions. This is one of the most reasoning-intensive analysis tasks in the development cycle. Sonnet is sufficient once findings are confirmed and implementation begins.

## Purpose

Bugs found proactively are far cheaper to fix than bugs found in production. This pass looks beyond what the current tests cover — focusing on the gaps, the edge cases, the assumptions that were never validated, and the code that works today but is fragile under changed conditions.

## Core Principle: Understand Before Fixing

Never fix a bug without fully understanding why it occurs and what else might be affected by the fix. A hasty fix in one place can silently break behavior elsewhere.

## Effort Estimation

Before starting, ask the user:

> "What's your Fibonacci estimate for this task? (1, 2, 3, 5, 8, 13...)"

**Fibonacci 1, 2, or 3 → Light Path.** **Fibonacci 5 and above → Full Cycle.**

### Light Path (Fibonacci 1, 2, or 3)

1. **Quick analysis** — read the relevant file(s) and understand the scope. No findings report.
2. **Implement** — make the focused change.
3. **Verify** — run `npm test ; npm run lint`. Both must pass.
4. **Scope check** — compare the actual change against the user's Fibonacci estimate. Confirm the fit in one or two sentences, or flag if the scope grew beyond what the estimate implied.
5. **Commit** — proceed to `dev-cycle-git`. The Fibonacci value is added to the Refactor Score as normal.

Skip the findings report, planning doc, documentation step, cleanup step, and formal review for light path work.

---

## When to Run

- After several feature cycles when complexity has accumulated
- When the codebase has grown into new areas with limited test coverage
- Before a major release or deployment
- When something has broken unexpectedly and a broader look is warranted
- As part of the regular refactor rotation

## This Skill Runs the Full Development Cycle

1. **dev-cycle-analysis** — with bug-hunting scope (see below)
2. **dev-cycle-planning** — plan which bugs to fix and in what order
3. **dev-cycle-implementation** — targeted fixes with new or updated tests
4. **dev-cycle-documentation** — document the bug, root cause, and fix
5. **dev-cycle-cleanup** — lint, audit, README
6. **dev-cycle-review** — verify fixes, check for regressions
7. **dev-cycle-git** — commit and push

## Bug Hunting Analysis Scope

During the analysis phase, examine the following across all relevant files:

### Error Handling Gaps
- Functions that can throw but have no try/catch or caller-level error handling
- Promises that are not awaited and have no `.catch()`
- Async functions where errors are silently swallowed
- Error paths that log but do not return or throw — allowing execution to continue in a broken state
- Missing null/undefined checks before property access or method calls
- Missing array bounds checks before index access

### Logic Errors
- Conditionals that look correct but have off-by-one errors
- Boolean logic that is inverted or incorrectly combined (common with `&&` vs `||`)
- Comparisons using `==` instead of `===` where type coercion could cause unexpected behavior
- Functions that mutate their arguments when the caller does not expect mutation
- Return values that are ignored when they carry error state or important data
- Early returns or breaks that skip logic that should always run

### Edge Cases
- Functions that assume non-empty input but receive empty arrays, empty strings, or zero
- Functions that assume valid input but receive null, undefined, NaN, or unexpected types
- Numeric operations that can produce Infinity, -Infinity, or NaN and propagate silently
- Date/time handling that does not account for timezone, DST, or locale differences
- String operations that do not account for encoding, special characters, or very long input
- Pagination or batching logic with off-by-one errors at boundaries

### State & Timing Issues
- Race conditions in async code where two operations assume exclusive access
- State that can be read while it is being written
- Event listeners that fire after a component or module is destroyed
- Caches or memoized values that are not invalidated when the underlying data changes
- Initialization order dependencies — code that assumes something is ready before it is

### Integration Points
- Functions that call external APIs or services without handling timeout, network failure, or unexpected response shapes
- Data passed between modules where the sending module and receiving module have different assumptions about shape or type
- Implicit contracts between modules that are not documented or enforced

### Test Coverage Gaps That Indicate Risk
- Functions with no tests at all
- Tests that only cover the happy path with no failure cases
- Tests that mock so heavily they do not test real behavior
- Areas that have changed frequently — high churn often correlates with fragility

## Findings Report

Save to `./docs/bug-hunting-[YYYY-MM-DD].md`:

```markdown
# Bug Hunting: [YYYY-MM-DD]

## Summary
[Overall assessment of code reliability and risk areas]

## Confirmed Bugs
[Logic errors or failure conditions that are definitively broken]

## High-Risk Fragility
[Code that works today but is likely to break under edge cases or changed conditions]

## Error Handling Gaps
[Uncaught errors, swallowed exceptions, missing null checks]

## Edge Cases Not Covered
[Input conditions or state transitions not handled or tested]

## State & Timing Concerns
[Race conditions, stale state, initialization order issues]

## Integration Risks
[Fragile assumptions between modules or external dependencies]

## Test Coverage Gaps
[Areas with no tests or tests that do not cover failure cases]

## Priority Recommendations
### Fix Now (confirmed bugs or high-probability failures)
### Fix Soon (fragile code likely to break under real conditions)
### Add Tests (areas that need coverage before they can be trusted)
### Monitor (low probability but worth tracking)
```

Present the full report to the user before any planning begins.

## Planning Guidance

When moving to `dev-cycle-planning`:
- Confirmed bugs are fixed first — they are already broken
- High-risk fragility is addressed next — it is broken under conditions that will eventually occur
- Each bug fix must include a test that would have caught the bug — if the bug existed without a test catching it, the test suite has a gap
- Group related bugs into cohesive phases — fixing one error handling gap often reveals adjacent ones

## Implementation Guidance

During `dev-cycle-implementation` for bug fixes:
- Write the failing test first when possible — confirm the test reproduces the bug before fixing it
- Fix the root cause — not just the symptom that surfaced
- After fixing, search for the same pattern elsewhere in the codebase
- Do not introduce new behavior beyond what is needed to fix the bug — scope the fix tightly

## Documentation Guidance

During `dev-cycle-documentation`:
- Add a comment to every bug fix explaining what the bug was, why it occurred, and what the fix does
- If the bug resulted from a non-obvious assumption, document the assumption explicitly so it is not re-introduced
- Format: `// Bug fix [YYYY-MM-DD] - [Author] - [what was wrong and why]`

## Hard Rules

- **Do not fix anything during analysis** — full findings report first
- **Every bug fix must have a test** that would have caught it — no fix without coverage
- **Do not fix the symptom** without understanding and addressing the root cause
- **Search for the same pattern** across the codebase before closing any finding
- **Do not introduce new behavior** as part of a bug fix — if a fix requires a design decision, surface it to the user
- **Confirm the bug exists** before fixing it — static analysis can suggest false positives, especially for state and timing issues

## Next Step

Once findings are reviewed and priorities agreed: proceed to **dev-cycle-planning** with bug hunting findings as input.

The git skill will reset `Refactor Score` to `0 / 34` in `ROADMAP.md` automatically when it detects this was a **full** refactor cycle (Fibonacci 5+). Light path refactor work (Fibonacci 1/2/3) adds to the score as normal.
