---
name: dev-cycle-documentation
description: Fourth step of the development cycle. Use this skill after implementation is complete and all tests are passing. Triggers when the user says things like "add comments", "document the changes", "update comments", "let's do documentation", or transitions from completed implementation. Requires dev-cycle-implementation to have been completed. Do not modify any logic or tests during this step.
---

# Development Cycle: Documentation

This is **Step 4** of the development cycle. Your job is to add or update comments on the code that was changed during implementation. No logic changes, no test changes, no refactoring.

## Purpose

Code comments serve as a record of intent, not just description. They help future readers (including you) understand why a decision was made, not just what the code does. Timestamps and author attribution create accountability and traceability.

## Prerequisites

- `dev-cycle-implementation` must be complete
- All tests must be passing before documentation begins
- Session plan file at `./docs/[task-name].md` should be in context

## Comment Audience Standard

Write every comment as if the next person reading this code is starting from scratch on the project but has solid coding experience. This means:
- Do not explain syntax or language basics
- Do explain *why* a decision was made, not just *what* the code does
- Do explain non-obvious relationships between this code and other parts of the project
- Do explain constraints, edge cases, and intentional limitations
- Assume the reader is capable but has zero prior context about this specific project

When in doubt, err toward more context rather than less. A comment that turns out to be obvious costs nothing. A missing comment on a subtle decision costs the next developer significant time.

## Comment Style

Use context to determine style — do not apply one format everywhere:

- **JSDoc (`/** */`)** — public functions, exported modules, class methods, anything consumed by other parts of the codebase or external callers. Include `@param`, `@returns`, and `@throws` where relevant.
- **Inline (`//`)** — internal logic, non-obvious decisions, workarounds, TODOs, hot path flags, and single-line clarifications inside function bodies.

If the file already has an established comment style, match it unless it conflicts with the above.

## What to Comment

### Always add or update:
- **Function/method headers** — what it does, parameters, return value, any side effects
- **Non-obvious logic** — if the reason for an approach isn't immediately clear, explain it
- **Hot path candidates** — if a function was flagged during analysis or touched during implementation, add a comment noting it as a candidate for runtime profiling
- **Workarounds or known limitations** — if something is intentionally imperfect, say so and why
- **TODO items** — if something from the Out of Scope log in the session plan is directly adjacent to changed code, add a `// TODO:` comment referencing it

### On changed or new functions — if no git repository:
Add attribution to every changed or new function:
```
// Modified: [YYYY-MM-DD] - [Your Name] - [brief reason for change]
```
or for new functions:
```
// Created: [YYYY-MM-DD] - [Your Name] - [brief description of purpose]
```

### On changed or new functions — if git repository exists:
Skip timestamps and author attribution. The commit message carries the reason and `git blame` carries the who/when more accurately than inline comments can.

### Do not comment:
- Self-explanatory code (`i++`, simple assignments, obvious returns)
- Every single line — over-commenting is as harmful as under-commenting
- Anything that was not touched during this implementation cycle

## How to Proceed

1. **Check for a git repository** — run `git status` in the project root. If it succeeds, the project has git and timestamps are not needed. If it fails (not a git repo), timestamps and author attribution apply for this session. Carry this determination through all steps below.

2. Read the session plan at `./docs/[task-name].md` to confirm which files and functions were changed
3. Review each changed file — add or update comments only on changed or new code
4. **If no git repository:** apply the timestamp and author attribution format to every changed or new function. Ask the user for their name if it is not already known — do not guess or leave it blank.
5. Flag any areas where intent was genuinely unclear during implementation so the user can provide the right comment language — do not invent intent
6. Present a summary of what was commented and where

## Hard Rules

- **Do not change any logic** — if you notice something wrong, log it and flag it; fix nothing
- **Do not change any tests** during this step
- **Do not refactor** — even small cleanups are out of scope here
- **Do not leave timestamps blank** — if the date is unknown, ask. (Applies only when no git repository is detected.)
- **Do not leave author name blank** — if the name is unknown, ask. (Applies only when no git repository is detected.)
- **Do not comment unchanged code** — only touch what was modified this session

## Next Step

Once all changed code is commented and attributed: proceed to **dev-cycle-cleanup**.
