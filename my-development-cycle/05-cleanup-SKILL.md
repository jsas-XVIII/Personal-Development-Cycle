---
name: dev-cycle-cleanup
description: Fifth step of the development cycle. Use this skill after documentation is complete. Triggers when the user says things like "run linting", "cleanup", "npm audit", "fix lint errors", "update README", or transitions from completed documentation. Requires dev-cycle-documentation to have been completed and all tests passing. Do not modify any logic or tests during this step.
---

# Development Cycle: Cleanup

This is **Step 5** of the development cycle. Your job is linting fixes, dependency audit, and README update. No logic changes, no new features, no refactoring.

## Purpose

Cleanup is deferred until after implementation and documentation are stable on purpose. Running linting during active coding creates noise. Running it once at the end against stable code is faster and produces cleaner results.

## Prerequisites

- `dev-cycle-documentation` must be complete
- All tests must still be passing before cleanup begins
- Session plan file at `./docs/[task-name].md` should be in context

## Steps — Run in This Order

### 1. Linting

Run the project's configured linter (check `package.json` scripts for the lint command):

```bash
npm run lint
```

For each lint error or warning:
- **Auto-fixable** — run `npm run lint -- --fix` or equivalent and apply fixes
- **Requires judgment** — present the issue and your recommended fix to the user before changing anything
- **Suppression** — do not add lint suppression comments (`// eslint-disable`) without explicit user approval. If a suppression seems warranted, explain why and ask first.

Re-run lint after fixes to confirm clean.

### 2. Dependency Audit

```bash
npm audit fix
```

- Apply fixes for low and moderate severity vulnerabilities automatically
- For high or critical severity vulnerabilities: present the issue, the suggested fix, and any known breaking change risks before applying
- If `npm audit fix --force` is suggested by npm, do not run it automatically — present it to the user first with a clear explanation of what `--force` means and what it may break
- Re-run `npm audit` after fixes to confirm status

### 3. Run Tests Again

After linting and audit fixes, re-run the full test suite to confirm nothing was broken by the cleanup changes:

```bash
npm test
```

If tests fail after cleanup:
- Identify whether the lint fix or audit fix caused the failure
- Fix the regression before proceeding
- Do not move forward with a broken test suite

### 4. Update README.md

Read the current `README.md` and update it to reflect changes made during this session:

- New features or functionality added
- Changed commands, scripts, or configuration
- New dependencies added or removed
- Any setup steps that changed
- Updated examples if relevant

Do not rewrite sections unrelated to this session's changes. Keep the README accurate to what the project actually does now.

### 5. Update Session Plan

Mark cleanup complete in `./docs/[task-name].md`.

## Hard Rules

- **Do not change logic** — if a lint rule exposes a real bug, flag it and log it as out of scope; do not fix it here
- **Do not run `npm audit fix --force`** without explicit user approval
- **Do not suppress lint warnings** without explicit user approval
- **Do not rewrite the README** — update only what changed this session
- **Do not proceed to git/push** if tests are failing after cleanup

## Next Step

Once linting is clean, audit is resolved, tests are passing, and README is updated: proceed to **dev-cycle-review**.
