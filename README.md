# Claude Code Development Cycle Skills

A set of structured skills for [Claude Code](https://code.claude.com) that encode a disciplined development cycle — from analysis through deployment, plus targeted refactor passes for ongoing code health.

> **Note:** These skills are actively being used and improved. They reflect a real workflow, not a theoretical one, and will evolve as that workflow does.

---

## Background

These skills came out of a need to bring structure to AI-assisted development without losing the judgment that makes software good. Claude Code is capable, but capability without discipline leads to scope creep, skipped steps, and silent compounding of technical debt — problems that are invisible until they become expensive.

The skills don't add capability. They direct Claude's judgment toward a specific quality bar, a specific workflow, and specific boundaries around when to stop and ask rather than decide alone. Each skill has one job, hard rules about what it does not do, and a clear handoff to the next step.

The development cycle encoded here prioritizes:
- Understanding before implementing
- Testing as part of implementation, not after
- Deferring work that creates noise during active coding (linting, comments, optimization)
- Human judgment at every point where static analysis reaches its limits

---

## How It Works

Skills are Markdown files that Claude Code reads to understand how to behave during a specific phase of work. Each skill in this set covers one stage of the development cycle. You invoke the relevant skill at the start of each phase, and Claude follows the rules encoded in it for that session.

For installation and setup, see the [Claude Code skills documentation](https://docs.claude.com).

---

## The Development Cycle

### Core Cycle

These seven skills form the standard development cycle. They run in order for every feature or coding task.

| # | Skill | Purpose |
|---|-------|---------|
| 01 | `dev-cycle-analysis` | Read relevant files, build context, identify hot path candidates, estimate complexity, verify test baseline. No changes yet. |
| 02 | `dev-cycle-planning` | Collaborate on what to build, write acceptance criteria (effort 5+), break into phases, assign effort score, confirm working branch, write session plan to `./docs/`. |
| 03 | `dev-cycle-implementation` | Write code phase by phase, run tests, diagnose failures. Defined abort protocol for structural plan failures. No comments yet. |
| 04 | `dev-cycle-documentation` | Add or update comments on changed code. Timestamps and author attribution on every changed function. |
| 05 | `dev-cycle-cleanup` | Lint fixes, `npm audit fix`, README update. Only after code and tests are stable. |
| 06 | `dev-cycle-review` | Systematic review of all changes before push. Claude's findings plus a prompt for your own pass. |
| 07 | `dev-cycle-git` | Commit message generation, branch description, push. Option to hand off git operations manually. |

### Refactor Cycle

These skills run periodically — not after every feature. Each one is independent: you pick whichever fits the current need and run it on its own. They do not need to be run together or in order. Each one runs through the full development cycle with a specific focus lens. A refactor score in `ROADMAP.md` tracks accumulated effort and suggests when a refactor pass is due.

| # | Skill | Focus |
|---|-------|-------|
| 1 | `dev-cycle-refactor-health-check` | Dead code, architectural drift, dependency health, technical debt inventory, documentation gaps. |
| 2 | `dev-cycle-refactor-efficiency` | Performance bottlenecks, hot path confirmation, algorithmic complexity, resource usage. |
| 3 | `dev-cycle-refactor-security` | Input handling, authentication gaps, data exposure, dependency vulnerabilities, common attack vectors. |
| 4 | `dev-cycle-refactor-bug-hunting` | Logic errors, edge cases, error handling gaps, state and timing issues, integration fragility. |
| 5 | `dev-cycle-refactor-test-coverage` | Untested code, undertested paths, over-mocked tests, weak assertions, integration gaps. |

---

## Project Conventions These Skills Assume

- `ROADMAP.md` in the project root — master feature tracker and refactor score
- `./docs/[task-name].md` — per-session plan files, created during planning, deleted after merge
- `npm test`, `npm run lint`, `npm audit` — standard npm scripts for testing, linting, and auditing
- `CLAUDE.md` in the project root — Claude Code project conventions file (if present, treated as source of truth)

> The npm/Node assumptions are specific to my current stack. If you are adapting these skills for a different stack, update the tooling references in the cleanup and analysis skills accordingly.

---

## Refactor Score

Each planning session assigns a Fibonacci effort score (1, 2, 3, 5, 8, 13, 21) to the work being done. This score accumulates in `ROADMAP.md` across sessions:

```
Refactor Score: 18 / 34
```

- **At 20:** Soft nudge to consider a refactor pass soon
- **At 34:** Strong recommendation to refactor before continuing

Completing any single refactor skill resets the score to 0 — you do not need to run all five. The reset is handled automatically by the git skill, which detects whether the session was a refactor cycle and resets instead of incrementing. This mirrors Agile story point sizing and gives a data-driven signal for when technical debt has accumulated enough to address — without requiring a refactor after every feature.

---

## Design Principles

**One job per skill.** Each skill covers exactly one phase. It does not drift into the next phase, and it has explicit rules about what it does not do.

**Flag, don't fix.** During analysis and review, Claude flags concerns without acting on them. Fixes happen in implementation, under a confirmed plan.

**Evidence before optimization.** The efficiency refactor skill distinguishes static hot path candidates from confirmed bottlenecks. Claude does not optimize without runtime evidence unless the structural case is overwhelming.

**Human judgment at the limits.** Static analysis cannot confirm performance bottlenecks, race conditions, or dynamic code references. The skills are explicit about where Claude's analysis ends and runtime observation or human review begins.

**Comments for the next person.** Documentation is written as if the next developer has solid coding experience but zero prior context about this specific project. The why matters more than the what.

---

## Status

These skills are in active use and will be updated as the workflow evolves. Known areas for future improvement are tracked in the repo issues.

Feedback, forks, and adaptations for other stacks are welcome.

---

## Author

Built through collaboration between personal development experience and Claude Code, as part of learning how to use AI tooling effectively without losing engineering discipline.
