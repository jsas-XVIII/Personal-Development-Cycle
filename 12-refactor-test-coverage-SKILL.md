---
name: dev-cycle-refactor-test-coverage
description: Refactor variant of the development cycle focused on identifying and filling gaps in automated test coverage. Use this skill when the user wants to improve test coverage, find untested code paths, or strengthen weak tests. Triggers when the user says things like "test coverage", "missing tests", "improve tests", "find untested code", "strengthen the test suite", "test coverage pass", or initiates a refactor session focused on testing. Always runs through the full development cycle. Do not write tests during analysis — produce findings first.
---

# Development Cycle: Refactor — Missing Test Coverage

This is a **refactor variant** of the development cycle focused on identifying gaps in automated test coverage and filling them with meaningful tests. The goal is a test suite that can be trusted — not just one that passes.

## Model Recommendation

> **Consider switching to Opus for the analysis phase of this skill.** Evaluating test quality requires reasoning about what a test actually proves, identifying what conditions are not covered, and understanding how modules interact in ways that unit tests alone may miss. Sonnet is sufficient for writing the tests once the gaps are identified.

## Purpose

A passing test suite is not the same as a trustworthy test suite. Tests can pass while covering only the happy path, mocking so heavily they test nothing real, or missing entire modules. This pass finds those gaps and fills them with tests that would actually catch failures.

## Core Principle: Tests Must Earn Trust

A test is only valuable if it would fail when the code it covers is broken. Tests that always pass regardless of what the code does are worse than no tests — they create false confidence.

## When to Run

- After several feature cycles when new code has been added without full coverage
- When bug hunting finds areas with no test coverage
- Before a major release or deployment
- When a bug slipped through that existing tests should have caught
- As part of the regular refactor rotation

## This Skill Runs the Full Development Cycle

1. **dev-cycle-analysis** — with test coverage scope (see below)
2. **dev-cycle-planning** — plan which gaps to fill and in what order
3. **dev-cycle-implementation** — write missing or improved tests
4. **dev-cycle-documentation** — update comments to reflect test coverage
5. **dev-cycle-cleanup** — lint, audit, README
6. **dev-cycle-review** — verify new tests are meaningful, check nothing regressed
7. **dev-cycle-git** — commit and push

## Test Coverage Analysis Scope

During the analysis phase, examine the following:

### Untested Code
- Functions or methods with no tests at all
- Modules that are imported and used but never directly tested
- New code added during recent feature cycles that was not accompanied by tests
- Code paths reachable only through specific conditions that tests never set up

### Undertested Code
- Functions tested only with a single happy path case
- Functions with multiple branches where only one branch is tested
- Functions that handle errors but have no tests for the error path
- Async functions tested only for successful resolution, never for rejection
- Functions with complex logic where tests only verify the final output, not intermediate behavior

### Test Quality Issues
- Tests that mock so heavily they do not test real behavior — if the implementation is completely wrong but the mocks return the right values, the test still passes
- Tests that test implementation details rather than behavior — these break whenever code is refactored even if behavior is unchanged
- Tests with assertions so broad they would pass even if the function returned the wrong thing (e.g., just checking that something is truthy)
- Tests that depend on execution order or shared mutable state
- Tests that never actually assert anything meaningful — setup and teardown but no real verification

### Integration Gaps
- Modules that interact with each other but are only tested in isolation
- API endpoints tested for happy path only, not for invalid input, missing auth, or error conditions
- Database operations tested with mocks but never against a real or test database
- External service integrations with no tests for failure, timeout, or unexpected response

### Edge Cases Not Tested
- Empty input (empty string, empty array, zero, null, undefined)
- Boundary values (first item, last item, maximum length, minimum value)
- Concurrent or rapid sequential calls to stateful functions
- Input at the limits of what the function claims to accept

## Coverage Tool Check

If a coverage tool is configured (e.g., `nyc`, `c8`, Jest coverage), run it and include the report in the analysis:

```bash
npm run test -- --coverage
```

Use coverage data to identify untested lines and branches — but note that line coverage alone is not sufficient. A line being executed is not the same as its behavior being verified. Flag areas with low coverage as starting points, not as the complete picture.

## Findings Report

Save to `./docs/test-coverage-[YYYY-MM-DD].md`:

```markdown
# Test Coverage Refactor: [YYYY-MM-DD]

## Summary
[Overall assessment of test suite quality and coverage]

## Coverage Report Summary
[If coverage tooling available: overall %, files below threshold, branches uncovered]

## Untested Code
[Functions, modules, or paths with no tests — with file references]

## Undertested Code
[Code with tests that do not cover failure paths, branches, or edge cases]

## Test Quality Issues
[Over-mocked tests, implementation-coupled tests, weak assertions]

## Integration Gaps
[Module interactions or API behaviors not covered by any test]

## Edge Cases Not Tested
[Specific input conditions or state scenarios missing from the test suite]

## Priority Recommendations
### Add Now (untested critical paths or code that has already caused bugs)
### Add Soon (undertested areas with real failure risk)
### Improve (existing tests that need strengthening)
### Monitor (low-risk gaps acceptable for now)
```

Present the full report to the user before any planning begins.

## Planning Guidance

When moving to `dev-cycle-planning`:
- Untested critical paths are filled first — these are the highest risk
- Each phase should focus on a cohesive area (e.g., all tests for one module, or all error path tests across the API layer)
- Do not try to reach 100% coverage in one session — prioritize meaningful coverage over line count
- For over-mocked or weakly asserted tests, plan rewrites not just additions

## Implementation Guidance

During `dev-cycle-implementation` for test writing:

### Writing Good Tests
- Test behavior, not implementation — a test should not break when the internal structure of a function changes but its behavior stays the same
- One concept per test — each test should verify one specific behavior or condition
- Descriptive test names — the test name should read as a plain-language statement of what is being verified: `"returns null when input array is empty"` not `"test case 3"`
- Arrange / Act / Assert structure — set up the condition, run the function, verify the result
- Test the failure path explicitly — do not just test that the happy path succeeds; test that errors are thrown, rejected, or handled correctly when they should be

### What Makes a Test Trustworthy
- If you changed the function to return the wrong thing, would this test fail? If no, the test is not earning its place.
- If you deleted the function entirely, would this test fail? If no, the mock coverage is too heavy.
- If the test passes without the function doing any real work, it needs to be rewritten.

### Mocking Guidance
- Mock external dependencies (network, filesystem, database) — not internal logic
- If mocking is required to test a function at all, consider whether the function is too tightly coupled and flag it
- Prefer test doubles that verify calls were made correctly, not just that they returned a value

## Hard Rules

- **Do not write tests during analysis** — full findings report first
- **Do not write tests that always pass** regardless of implementation — they are worse than no tests
- **Do not chase line coverage** at the expense of meaningful coverage — a well-tested 70% is better than a poorly tested 95%
- **Do not mock internal logic** — only mock genuine external dependencies
- **Every new test must have a descriptive name** that reads as a plain-language behavior statement
- **Verify each new test fails** when the code it covers is broken before committing it — a test that never fails proves nothing

## Next Step

Once findings are reviewed and priorities agreed: proceed to **dev-cycle-planning** with test coverage findings as input.

The git skill will reset `Refactor Score` to `0 / 34` in `ROADMAP.md` automatically when it detects this was a refactor cycle.
