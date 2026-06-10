---
name: dev-cycle-refactor-efficiency
description: "[Refactor 2/5 — runs full 7-step cycle] Refactor variant of the development cycle focused on performance and efficiency. Use this skill when the user wants to improve speed, reduce resource usage, or address known performance concerns. Triggers when the user says things like "performance refactor", "efficiency pass", "optimize the code", "it's running slow", "let's look at performance", "profile the hot paths", or initiates a refactor session focused on speed or resource usage. Always runs through the full development cycle. Do not optimize anything during analysis — produce findings first.
---

# Development Cycle: Refactor — Efficiency & Performance

This is a **refactor variant** of the development cycle focused on identifying and addressing performance bottlenecks and inefficiencies. The goal is measurable improvement based on evidence, not premature optimization based on assumption.

## Purpose

Performance problems that are invisible during development become real under load or with real data. This refactor pass systematically identifies where time and resources are being spent, distinguishes confirmed bottlenecks from candidates, and addresses them with targeted changes.

## Model Recommendation

> **Consider switching to Opus for the analysis phase of this skill.** Identifying non-obvious performance patterns, algorithmic complexity issues, and resource leaks across multiple files benefits from deeper reasoning. Once findings are confirmed and planning begins, Sonnet is sufficient for the remaining steps.

## Core Principle: Evidence Before Optimization

**Never optimize based on assumption alone.** Static analysis identifies candidates. Runtime profiling confirms bottlenecks. Optimizing the wrong thing wastes time and introduces risk.

The workflow is:
1. Static analysis to identify candidates
2. Runtime profiling to confirm actual bottlenecks (user-driven)
3. Plan targeted fixes for confirmed bottlenecks
4. Measure improvement after changes

## When to Run

- After several feature cycles when the codebase has grown
- When users or testing reveal slowness or high resource usage
- When hot path candidates have accumulated across previous sessions
- Before scaling or deploying to production

## This Skill Runs the Full Development Cycle

1. **dev-cycle-analysis** — with performance-focused scope (see below)
2. **dev-cycle-planning** — plan which bottlenecks to address
3. **dev-cycle-implementation** — targeted optimizations only
4. **dev-cycle-documentation** — update comments, note optimizations made
5. **dev-cycle-cleanup** — lint, audit, README
6. **dev-cycle-review** — verify improvements, check for regressions
7. **dev-cycle-git** — commit and push

## Performance Analysis Scope

During the analysis phase, extend the standard analysis to cover:

### Hot Path Candidates (Static)
- Nested loops, especially those iterating over large or unbounded data
- Recursive functions without memoization or depth limits
- Functions called on every render, every request, or in tight loops
- Database or file I/O inside loops
- Synchronous operations that block the event loop
- Large in-memory data transformations without streaming or pagination
- Repeated computation of values that could be cached
- Unnecessary re-instantiation of objects or connections inside loops

### Previously Flagged Candidates
- Review all `// TODO: profile` or hot path comments left during previous sessions
- Collect and list them — these are the highest priority candidates since they were already identified as concerns

### Algorithmic Complexity
- Functions whose complexity grows non-linearly with input size
- Sorting or searching operations on large datasets without indexing
- String concatenation in loops (should use array join or buffer)
- Array methods chained in ways that iterate the full dataset multiple times

### Resource Usage
- Memory leaks — event listeners not removed, closures holding references, growing caches with no eviction
- Connections not properly closed or pooled
- Large objects held in memory longer than needed

## Runtime Profiling Checkpoint

After static analysis, before planning:

Present the list of hot path candidates and explicitly ask the user:

> "These are static candidates — I cannot confirm which are actual bottlenecks without runtime data. Before we plan fixes, do you have profiling data, performance benchmarks, or observed slowness that can help us prioritize? If not, I can suggest which candidates are most likely to be impactful based on their structure."

If the user has runtime data, use it to prioritize. If not, rank candidates by:
1. Candidates inside the most frequently executed paths
2. Candidates with worst-case complexity (O(n²) or worse)
3. Previously flagged candidates from prior sessions
4. Resource leak candidates (memory leaks compound over time)

## Findings Report

Save to `./docs/performance-refactor-[YYYY-MM-DD].md`:

```markdown
# Performance Refactor: [YYYY-MM-DD]

## Summary
[Overall assessment of performance posture]

## Confirmed Bottlenecks
[From runtime profiling data if available]

## Hot Path Candidates (Static Analysis)
[Ranked list with file/line references and complexity notes]

## Resource Usage Concerns
[Memory, connection, or I/O issues identified]

## Previously Flagged Candidates
[Collected from TODO/hot path comments in codebase]

## Profiling Recommendations
[Specific suggestions for what to profile and how, for candidates that need runtime confirmation]

## Priority Recommendations
### Address Now (confirmed or high-confidence)
### Address After Profiling (candidates needing runtime confirmation)
### Monitor (low risk, low priority)
```

## Planning Guidance

When moving to `dev-cycle-planning`:
- Confirmed bottlenecks get planned first
- Each optimization should be a discrete phase — do not bundle multiple optimizations together
- Each phase must include a before/after measurement plan so improvement is verifiable
- Do not optimize a candidate that has not been confirmed unless the structural evidence is overwhelming

## Implementation Guidance

During `dev-cycle-implementation` for performance changes:
- Establish a baseline measurement before making changes (benchmark, timing log, or profiler snapshot)
- Make one optimization at a time — do not batch multiple changes into one phase
- Measure after each change to confirm improvement and catch regressions
- If an optimization does not produce measurable improvement, revert it — complexity is not worth it without gain
- Add a comment to every optimized function explaining what was changed and why

## Hard Rules

- **Do not optimize without evidence** — candidate is not the same as confirmed bottleneck
- **Do not bundle multiple optimizations** into a single phase — isolate changes so regressions are traceable
- **Do not sacrifice readability** for marginal performance gains without explicit user approval
- **Do not remove a hot path comment** unless the candidate has been profiled and confirmed not a bottleneck
- **Measure before and after** every optimization — if improvement is not measurable, reconsider the change

## Next Step

Once findings are reviewed and priorities agreed: proceed to **dev-cycle-planning** with performance findings as input.

The git skill will reset `Refactor Score` to `0 / 34` in `ROADMAP.md` automatically when it detects this was a refactor cycle.
