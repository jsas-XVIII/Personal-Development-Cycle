---
name: dev-cycle-refactor-health-check
description: "[Refactor 1/5 — runs full 7-step cycle] Refactor variant of the development cycle focused on overall project health. Use this skill when the user wants to audit the project holistically rather than work on a specific feature. Triggers when the user says things like "health check", "project audit", "check the project", "what's the state of the codebase", "technical debt review", "let's do a health check", or initiates a refactor session without a specific bug or feature in mind. Runs through the full development cycle with a health-focused lens. Do not fix anything during the analysis phase — produce a findings report first.
---

# Development Cycle: Refactor — Health Check

This is a **refactor variant** of the development cycle. It zooms out from individual features to assess the overall state of the project. The goal is a prioritized findings report that feeds into future planning cycles — not immediate fixes.

## Purpose

Feature development accumulates invisible debt: dead code, architectural drift, stale dependencies, documentation gaps, and untested areas. None of these break anything today, but they compound over time. The health check makes the invisible visible so it can be addressed deliberately.

## Model Recommendation

> **Consider switching to Opus before starting this skill.** The health check requires broad cross-file pattern recognition, architectural judgment, and technical debt assessment across the entire project. Sonnet is sufficient for execution steps later in the cycle, but the analysis phase benefits significantly from Opus on medium to large projects.

## When to Run

- Every few features or major milestones
- When the codebase starts feeling harder to work in than it used to
- Before a significant new feature that will touch many parts of the project
- When onboarding someone new to the project

## This Skill Runs the Full Development Cycle

Health check findings are addressed through the standard cycle:
1. **dev-cycle-analysis** — with health-check scope (see below)
2. **dev-cycle-planning** — plan which findings to address and in what order
3. **dev-cycle-implementation** — fix what was planned
4. **dev-cycle-documentation** — update comments affected by changes
5. **dev-cycle-cleanup** — lint, audit, README
6. **dev-cycle-review** — review before push
7. **dev-cycle-git** — commit and push

The health check itself happens during the analysis phase. Do not skip to fixing things — produce the full findings report first, then plan which items to address.

## Health Check Analysis Scope

During the analysis phase, extend the standard analysis to cover the following areas across the entire project:

### Dead Code
- Functions, methods, or classes defined but never called
- Exported values that are never imported anywhere
- Files that are never referenced
- Commented-out code blocks that have been left indefinitely

### Architectural Drift
- Patterns that were established early but are no longer followed consistently
- Modules that have grown beyond their original responsibility
- Circular dependencies or unexpected coupling between modules
- Inconsistent naming conventions across the codebase
- Areas where the structure no longer matches the intent described in `ROADMAP.md` or `CLAUDE.md`

### Dependency Health
- Packages in `package.json` that are significantly outdated
- Dependencies with known vulnerabilities beyond what `npm audit` surfaces in a single session
- Packages that are no longer used or have better alternatives
- Dev dependencies that have leaked into production or vice versa

### Test Coverage Gaps
- Modules or functions with no test coverage at all
- Tests that exist but only cover the happy path
- Integration points between modules that have no tests
- Areas flagged as risky during previous sessions that still lack coverage

### Documentation Gaps
- Public functions or modules with no JSDoc
- README sections that are outdated or missing
- `ROADMAP.md` items that are complete but not marked done
- Comments that no longer match the code they describe
- Missing or stale `CLAUDE.md` content

### Technical Debt Inventory
- TODO comments in the codebase — collect and summarize them
- Known shortcuts or workarounds noted in comments
- Areas where a quick fix was applied that needs a proper solution
- Hot path candidates that have never been profiled

## Findings Report

After analysis, produce a structured findings report before any planning begins. Save it to `./docs/health-check-[YYYY-MM-DD].md`:

```markdown
# Health Check: [YYYY-MM-DD]

## Summary
[2-4 sentence overall assessment of project health]

## Findings by Area

### Dead Code
[List of findings with file and line references]

### Architectural Drift
[List of findings with file references and description of drift]

### Dependency Health
[List of outdated, unused, or vulnerable packages]

### Test Coverage Gaps
[List of untested or undertested areas]

### Documentation Gaps
[List of missing or stale documentation]

### Technical Debt Inventory
[Collected TODOs, known shortcuts, unresolved hot path candidates]

## Priority Recommendations
### Address Now
[Findings that pose real risk or significantly slow development]

### Address Soon
[Findings that are accumulating but not yet critical]

### Address Later / Monitor
[Low-priority findings worth tracking but not urgent]

## Suggested Next Sessions
[Recommended refactor or feature sessions based on findings — feeds into ROADMAP.md]
```

Present the report to the user and discuss priorities before moving to planning. Do not plan or fix anything until the user has reviewed the full report.

## Planning Guidance for Health Check

When moving to `dev-cycle-planning` after the health check:
- Not all findings need to be addressed in one session — pick a manageable scope
- Address Now items should be planned first
- Group related findings into cohesive phases (e.g., dead code removal is one phase, dependency updates another)
- Anything not addressed this session goes into `ROADMAP.md` as future tasks

## Hard Rules

- **Do not fix anything during the health check analysis** — findings first, plan second
- **Do not address all findings at once** — scope the work realistically
- **Do not delete dead code without confirming** it is truly unreferenced — Claude's static analysis can miss dynamic references
- **Do not update dependencies** without checking for breaking changes first
- **Confirm dynamic references** before removing anything — search for string-based requires, dynamic imports, and runtime-constructed paths

## Next Step

Once the findings report is reviewed and priorities are agreed: proceed to **dev-cycle-planning** with the health check findings as input.

The git skill will reset `Refactor Score` to `0 / 34` in `ROADMAP.md` automatically when it detects this was a refactor cycle.
