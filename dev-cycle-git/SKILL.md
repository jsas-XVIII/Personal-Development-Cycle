---
name: dev-cycle-git
description: "[Step 7/7 — Core Cycle] Seventh and final step of the development cycle. Use this skill after review is complete and the user is ready to commit and push changes to the repository. Triggers when the user says things like "push the changes", "commit", "let's push", "generate a branch description", or transitions from completed review. Requires dev-cycle-review to have been completed and the user to have confirmed they are satisfied with the changes.
---

# Development Cycle: Git & Push

This is **Step 7** of the development cycle. Your job is to commit the changes, generate a clear branch description, and push to the repository. Everything after the push is handled by the user.

## Purpose

A clean commit with a well-written description makes the repository history useful. Vague commit messages and branch descriptions create confusion during code review, debugging, and future refactoring.

## Prerequisites

- `dev-cycle-review` must be complete
- User must have confirmed they are satisfied with the changes
- All tests must be passing
- Linting must be clean
- No must-fix items from review left unresolved

## Manual Handoff Option

Before proceeding, ask the user:

> "Would you like to handle the commit and push manually, or should I walk through it?"

**If the user wants to handle it manually:**
- Generate the commit message and branch description (steps 3 and 5 below) and present both
- Update `ROADMAP.md` and remind the user to delete the session plan file
- Hand off — do not proceed with any git commands

**If the user wants Claude to handle it:**
- Proceed through all steps below in order

---

## Steps — Run in This Order

### 1. Confirm Status

Run a final status check before touching git:

```bash
npm test
git branch --show-current
git status
git diff --stat
```

**If the current branch is `main` or `master`: stop immediately.** Do not stage or commit anything. Inform the user and ask them to switch to the correct feature branch before proceeding. This is a hard stop — pushing a full feature session directly to main is not recoverable without a force push.

Present the diff summary and current branch to the user so they can confirm the right files are being committed to the right branch. Do not proceed if anything looks unexpected.

### 2. Stage Changes

Stage all changes related to this session:

```bash
git add .
```

If there are untracked files that should not be committed (e.g., local config, temp files, build artifacts), flag them to the user before staging. Confirm `.gitignore` is covering what it should.

### 3. Generate Commit Message

Write a commit message following this structure:

```
[type]: [short summary of what changed] (50 chars or less)

- [specific change 1]
- [specific change 2]
- [specific change 3]
...

Tests: [passing / updated / added]
```

**Type prefixes:**
- `feat` — new feature or functionality
- `fix` — bug fix
- `refactor` — code change that doesn't add features or fix bugs
- `test` — adding or updating tests only
- `docs` — documentation or comment changes only
- `chore` — cleanup, linting, dependency updates

Present the commit message to the user for approval before committing. If they want changes, revise and re-present.

### 4. Commit

```bash
git commit -m "[approved message]"
```

### 5. Generate Branch Description

Before pushing, generate a plain-language description of what this branch contains. This is what the user will use for their pull request or code review submission.

Structure:

```
## What Changed
[2-4 sentences describing what was built or fixed and why]

## Phases Completed
[List of phases from the session plan, briefly described]

## Tests
[What was tested, what was added or updated]

## Notes
[Any out-of-scope items logged during the session, hot path candidates flagged for runtime profiling, known limitations, or follow-up work needed]
```

Present this to the user for review. They will use it — do not treat it as internal notes.

### 6. Push

```bash
git push origin [branch name]
```

Confirm the correct branch name before pushing. If the branch does not exist on the remote yet, use:

```bash
git push -u origin [branch name]
```

### 7. Update ROADMAP.md

After a successful push, update `ROADMAP.md` in the project root:
- Mark the relevant item as done with the completion date
- Add any follow-up items from the Out of Scope log as future tasks if they are significant enough to track at the roadmap level — ask the user before adding them

**Refactor Score Update:**

First, determine whether this session was a **refactor cycle** (triggered by skills 08–12) or a **standard feature cycle**.

**If this was a refactor cycle:** Reset the score to zero:

```
Refactor Score: 0 / 34
```

**If this was a standard feature cycle:** Read the current `Refactor Score` from `ROADMAP.md` and add the session's approved Fibonacci value from the session plan file:

```
Refactor Score: [current + session value] / 34
```

Then check the updated score against these thresholds and surface the appropriate message:

| Score | Action |
|-------|--------|
| Below 20 | No message — update silently |
| 20–33 | Soft nudge: "Refactor score is at [X]. You may want to consider a refactor pass in the next session or two depending on what's coming up." |
| 34+ | Strong recommendation: "Refactor score has reached [X]. Technical debt is accumulating — recommend scheduling a refactor before continuing with new features." |

Do not pressure the user to refactor — present the score and recommendation, then let them decide.

### 8. Clean Up Session Plan

Remind the user that `./docs/[task-name].md` can be deleted now that the task is complete and pushed. Do not delete it automatically — let the user confirm.

## Hard Rules

- **Do not push without user confirmation of the diff summary**
- **Do not push if tests are failing**
- **Do not write vague commit messages** — "fixed stuff" or "updates" are not acceptable
- **Do not modify ROADMAP.md** before the push is confirmed successful
- **Do not delete the session plan file** — remind the user and let them do it
- **Everything after the push is the user's responsibility** — do not attempt to open PRs, merge branches, or interact with the remote repository beyond pushing

## Next Step

This is the end of the standard development cycle. The next session begins with **dev-cycle-analysis**.

For periodic refactoring and health checks, proceed to the appropriate refactor skill.
