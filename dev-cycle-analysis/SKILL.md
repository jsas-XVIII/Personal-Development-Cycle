---
name: dev-cycle-analysis
description: "[Step 1/7 — Core Cycle] First step of the development cycle. Use this skill at the start of any coding session, before planning, implementing, or making any changes. Triggers when the user says things like "let's start a new session", "I want to work on X", "read the project", "get context", or begins describing a feature or change they want to make. This skill must run before dev-cycle-planning. Do not suggest any changes during this step.
---

# Development Cycle: Analysis

This is **Step 1** of the development cycle. Your only job here is to read and understand. Do not suggest changes, improvements, or implementations yet.

## Purpose

Build full context of the project or relevant code before any planning begins. Skipping this step leads to plans based on assumptions rather than reality, which risks over-engineering and incorrect implementations.

## What to Read

Determine scope based on what the user describes:

**For a new feature or change:**
- `CLAUDE.md` or `README.md` for project overview and conventions
- Files directly related to the area being changed
- Test files associated with those files
- Any config files relevant to the change (e.g., routes, schemas, env)

**For a refactor or health check:**
- Full project file tree first (`ls` or equivalent)
- All source files in scope
- Existing test coverage
- `package.json` for dependencies and scripts
- Any existing TODOs or known issues in comments

**Always read:**
- `CLAUDE.md` if it exists — this is the source of truth for project conventions

## How to Proceed

1. Ask the user what they want to work on if not already clear
2. Read the relevant files silently — do not summarize each file as you go
3. When done, provide a context summary covering:

   **Overview**
   - What the relevant code currently does
   - How the files relate to each other

   **Hot Path Candidates** *(static analysis only — runtime profiling required to confirm)*
   - Nested loops or recursion
   - Functions called from many locations
   - Large data transformations without pagination or batching
   - Synchronous operations that could block
   - Anything that grows with input size in a non-obvious way
   - Note clearly: these are *candidates*, not confirmed bottlenecks. Flag for runtime diagnostics if optimization is planned.

   **Concerns**
   - Missing or weak tests
   - Unclear patterns or naming
   - Outdated comments
   - Anything that looks risky — flagged only, not fixed

   **Estimated Complexity**
   - Low / Medium / High / Very High
   - Brief rationale (e.g., "Medium — isolated change to one module with good test coverage" or "High — touches auth flow, shared utilities, and has no integration tests")
   - If High or Very High, recommend breaking the work into smaller phases during planning

   **Test Baseline**
   - Result of running `npm test` before any changes are made
   - Number of passing and failing tests
   - If tests are failing: list which ones and note clearly that these are pre-existing failures — they must not be confused with failures introduced during implementation

4. **Run the test suite** to establish the baseline:

   ```bash
   npm test
   ```

   - If all tests pass: note the count and proceed
   - If any tests are failing: present them clearly to the user. Do not proceed to planning until the user explicitly acknowledges the pre-existing failures and confirms how to handle them. Options are: fix them before starting the session, note them as known failures to ignore during the test loop, or cancel the session.

5. Confirm with the user that context is sufficient before moving to planning

## Hard Rules

- **Do not suggest any code changes during this step**
- **Do not enter plan mode yet**
- **Do not fix anything you notice** — log it, flag it, move on
- If something looks broken or risky, note it clearly so it surfaces in planning
- **Do not proceed to planning if tests are failing** without explicit user acknowledgement — pre-existing failures corrupt the implementation test loop if not accounted for

## Next Step

Once context is confirmed: proceed to **dev-cycle-planning**.
