---
name: dev-cycle-planning
description: "[Step 2/7 — Core Cycle] Second step of the development cycle. Use this skill after dev-cycle-analysis has been completed and the user is ready to plan what to build or change. Triggers when the user says things like "let's plan", "what should we do", "how should we approach this", "break this down", or moves from describing the project into deciding what to do next. Do not write any code during this step. This skill must run before dev-cycle-implementation.
---

# Development Cycle: Planning

This is **Step 2** of the development cycle. Your job is collaborative design and phase breakdown. No code is written here.

## Purpose

Establish a clear, agreed-upon plan before any implementation begins. Unplanned implementation leads to over-engineering, scope creep, and code that solves the wrong problem. The plan produced here directly drives the implementation step.

## Prerequisites

- `dev-cycle-analysis` must have been completed
- Complexity estimate and any flagged concerns from analysis should be in context
- If analysis was skipped, stop and ask the user to run analysis first

## How to Proceed

1. **Enter plan mode** — use Claude Code's plan mode for this entire step. Do not exit plan mode until the user confirms the plan.

2. **Restate the goal** — in one or two sentences, confirm your understanding of what the user wants to accomplish. Ask for correction if needed before going further.

3. **Write acceptance criteria (effort 5+ only)** — before breaking into phases, define 3–5 behavioral conditions that must be true when the task is complete. Write these as testable statements from the user's perspective, not implementation details. Example: "Given an empty list, when the user submits, the form shows a validation error." These anchor the phases — if a phase doesn't serve any criterion, question whether it belongs.

4. **Propose phases** — break the work into phases based on:
   - What code is touched (keep changes to related files grouped)
   - Priority (core functionality before edge cases)
   - Risk (lower-risk changes before higher-risk ones)
   - Dependency order (what must exist before something else can be built)

5. **For each phase, define:**
   - What changes are being made
   - Which files are affected
   - What the success criteria is (how do we know this phase is done)
   - Whether automated tests need to be written or updated
   - Any hot path candidates from analysis that are relevant to this phase

6. **Flag scope boundaries** — if something comes up during planning that is outside the current goal, note it explicitly as out of scope. Do not expand the plan to include it. Log it for a future session.

7. **Adjust for complexity:**
   - Low/Medium: single phase is acceptable if changes are cohesive
   - High: minimum two phases, core changes separated from edge cases
   - Very High: push back on attempting all at once — recommend the user pick a subset to tackle first

8. **Present the full plan** and ask for confirmation before proceeding

9. **Confirm working branch** — after the plan is confirmed, check the current branch before writing the session plan:
   - Run `git branch --show-current`
   - If on `main` or `master`: stop. Do not proceed to implementation on this branch. Ask the user to confirm or create a feature branch. Suggest a name derived from the task using the same type prefix as the planned commit type — e.g., `feat/add-user-auth`, `fix/null-check-on-submit`, `refactor/extract-auth-middleware`.
   - If already on a feature branch: confirm it matches the current task. If the branch name looks unrelated, flag it and ask the user to confirm before proceeding.
   - Record the confirmed branch name in the session plan file.

## Collaboration Rules

- This is a conversation, not a monologue — ask questions when intent is unclear
- If the user's proposed approach has a risk or flaw, say so directly with reasoning
- Offer alternatives when pushing back, not just objections
- Do not gold-plate — the simplest plan that meets the goal is the right plan
- If a hot path candidate from analysis overlaps with planned changes, flag it so the user can decide whether runtime diagnostics should be part of the plan

## Hard Rules

- **Do not write any code during this step**
- **Do not exit plan mode until the plan is confirmed**
- **Do not expand scope** — if something new surfaces, log it and stay focused
- **Do not skip phases to save time** — if the complexity warrants phases, keep them

## Output

When the plan is confirmed, create a session plan file at `./docs/[task-name].md` containing:

```markdown
# Plan: [task name]
Date: [date]
Branch: [branch name]

## Effort Score
[Approved Fibonacci value for this session]

## Goal
[One or two sentence summary of what this session accomplishes]

## Acceptance Criteria
*(Effort 5+ only — omit for lower scores)*
- [ ] [Behavioral condition 1]
- [ ] [Behavioral condition 2]
- [ ] [Behavioral condition 3]

## Phases
### Phase 1: [name]
- Files affected:
- Changes:
- Success criteria:
- Tests: [new / update existing / none needed]

### Phase 2: [name]
...

## Hot Path Candidates
[From analysis — any candidates relevant to this task. Note: static analysis only, runtime profiling required to confirm.]

## Flagged Concerns
[From analysis — missing tests, risky patterns, outdated comments relevant to this task]

## Out of Scope
[Items that surfaced during planning but are not part of this task — log for future session]
```

Also read `ROADMAP.md` in the project root to understand where this task fits in the larger project. If `ROADMAP.md` does not yet contain a `Refactor Score` line, add one during planning:

```
Refactor Score: 0 / 34
```

Otherwise, do not modify `ROADMAP.md` during planning — it is updated only at the end of the cycle.

## Effort Scoring

During phase breakdown, estimate the effort for this session using Fibonacci sizing:

| Value | Meaning |
|-------|---------|
| 1 | Trivial — single file, no logic change |
| 2 | Small — simple change, low risk |
| 3 | Minor — a few files, straightforward |
| 5 | Moderate — multiple files, some complexity |
| 8 | Significant — cross-file changes, meaningful complexity |
| 13 | Large — broad impact, high complexity |
| 21 | Very large — consider breaking into smaller sessions |

Propose the score to the user during planning and get approval before proceeding. Record the approved value in the session plan file — it will be applied to `ROADMAP.md` at the end of the cycle during the git step.

## Next Step

Once the plan is confirmed and `./docs/[task-name].md` is written: proceed to **dev-cycle-implementation**.
