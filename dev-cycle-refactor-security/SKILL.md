---
name: dev-cycle-refactor-security
description: "[Refactor 3/5 — light path for Fibonacci 1/2/3, full 7-step cycle for Fibonacci 5+] Refactor variant of the development cycle focused on security vulnerabilities and unsafe patterns. Use this skill when the user wants to audit the codebase for security issues, unsafe practices, or exposure risks. Triggers when the user says things like "security refactor", "security audit", "check for vulnerabilities", "security pass", "look for security issues", "harden the code", or initiates a refactor session focused on security. Always runs through the full development cycle. Do not fix anything during analysis — produce findings first.
---

# Development Cycle: Refactor — Security

This is a **refactor variant** of the development cycle focused on identifying and addressing security vulnerabilities, unsafe patterns, and exposure risks. The goal is a thorough findings report followed by targeted, verified fixes.

## Model Recommendation

> **Consider switching to Opus for the analysis phase of this skill.** Security analysis requires recognizing subtle vulnerability patterns across multiple files, understanding how data flows through the application, and reasoning about attack vectors that are not always structurally obvious. Sonnet is sufficient for implementation and cleanup once findings are confirmed.

## Purpose

Security vulnerabilities are often invisible during feature development — they hide in input handling, dependency choices, authentication logic, and data exposure patterns. A dedicated security pass finds what normal development misses.

## Core Principle: Fix With Understanding

Never patch a security issue without understanding the root cause. A surface-level fix that does not address the underlying pattern leaves the codebase vulnerable in adjacent areas.

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

- Before deploying to production or a public environment
- After adding new user-facing input handling or authentication features
- After significant dependency updates
- Periodically as part of the refactor cycle
- When a security concern surfaces during normal development

## This Skill Runs the Full Development Cycle

1. **dev-cycle-analysis** — with security-focused scope (see below)
2. **dev-cycle-planning** — plan which vulnerabilities to address
3. **dev-cycle-implementation** — targeted security fixes only
4. **dev-cycle-documentation** — document security decisions and mitigations
5. **dev-cycle-cleanup** — lint, audit, README
6. **dev-cycle-review** — verify fixes, check for regressions
7. **dev-cycle-git** — commit and push

## Security Analysis Scope

During the analysis phase, examine the following across all relevant files:

### Input Handling
- User input that reaches the database without sanitization or parameterization (SQL injection risk)
- User input that reaches HTML output without escaping (XSS risk)
- User input used in file paths, shell commands, or eval (injection risk)
- Missing or insufficient input validation on API endpoints or form handlers
- Accepting input types broader than needed (e.g., accepting any string where only an integer is valid)

### Authentication & Authorization
- Endpoints or functions that should require authentication but do not check for it
- Authorization checks that verify identity but not permission (authn without authz)
- Hardcoded credentials, API keys, tokens, or secrets anywhere in the codebase
- Secrets or credentials in comments, logs, or error messages
- JWT or session handling that does not validate properly
- Password handling that does not use appropriate hashing (bcrypt, argon2, etc.)

### Data Exposure
- Sensitive data returned in API responses that should be filtered out
- Sensitive data written to logs
- Error messages that expose internal implementation details, stack traces, or file paths to the client
- Personal or sensitive data stored without encryption where encryption is appropriate

### Dependency Vulnerabilities
- Known vulnerabilities in `package.json` dependencies beyond what the most recent `npm audit` covered
- Packages that are significantly outdated and have known CVEs
- Packages with suspicious or overly broad permissions
- Transitive dependencies with known issues

### Common Vulnerability Patterns
- Prototype pollution risks in object merging or assignment
- Regular expressions vulnerable to ReDoS (catastrophic backtracking)
- Insecure use of `eval`, `Function()`, or `setTimeout` with string arguments
- Missing rate limiting on sensitive endpoints (login, password reset, API)
- CORS configuration that is too permissive
- Missing or misconfigured security headers
- Insecure direct object references (using user-supplied IDs to access records without ownership checks)

### Environment & Configuration
- `.env` files or secrets that could be accidentally committed
- Environment variables used in client-side code that should be server-side only
- Debug mode or verbose logging enabled in production configuration
- Overly permissive file or directory permissions

## Findings Report

Save to `./docs/security-refactor-[YYYY-MM-DD].md`:

```markdown
# Security Refactor: [YYYY-MM-DD]

## Summary
[Overall security posture assessment]

## Critical Findings
[Vulnerabilities that pose immediate risk — must address before next deployment]

## High Findings
[Significant vulnerabilities that should be addressed soon]

## Medium Findings
[Vulnerabilities with lower exploitability or impact]

## Low / Informational
[Best practice gaps, minor exposure risks, things worth monitoring]

## Dependency Vulnerabilities
[Packages with known CVEs or significant version lag]

## Recommended Fixes
[For each finding: root cause, recommended fix approach, and whether adjacent code has the same pattern]

## Out of Scope / Needs Further Investigation
[Items requiring runtime analysis, penetration testing, or external review]
```

Present the full report to the user before any planning begins. Do not start fixing until priorities are confirmed.

## Planning Guidance

When moving to `dev-cycle-planning`:
- Critical findings must be addressed before any deployment — plan these first
- Address root causes, not just surface symptoms — if the same unsafe pattern appears in multiple places, fix the pattern
- Each finding or related group of findings should be a discrete phase
- Some findings may require architectural changes — scope these carefully and do not underestimate them

## Implementation Guidance

During `dev-cycle-implementation` for security fixes:
- Fix the root cause, not just the specific instance — search for the same pattern elsewhere and fix it consistently
- Add tests that verify the vulnerability is fixed (e.g., a test that confirms sanitized input is rejected, that an unauthorized request returns 403, etc.)
- Do not introduce new dependencies to fix security issues without checking that dependency's own security posture

## Documentation Guidance

During `dev-cycle-documentation`:
- Document every security decision — why a particular approach was chosen, what it protects against
- Add comments to authentication and authorization checks explaining what is being enforced and why
- Note any known limitations of the fix if full mitigation was not possible

## Hard Rules

- **Do not fix anything during analysis** — full findings report first
- **Do not patch surface symptoms** without addressing the root cause pattern
- **Do not commit hardcoded secrets** under any circumstances — if found, remove from code and rotate the credential
- **Do not log sensitive data** as part of a debug fix during implementation
- **Do not mark a critical finding as deferred** without explicit user confirmation and a clear plan for when it will be addressed
- **Search for the same pattern across the codebase** before closing any finding — one instance usually means more

## Next Step

Once findings are reviewed and priorities agreed: proceed to **dev-cycle-planning** with security findings as input.

The git skill will reset `Refactor Score` to `0 / 34` in `ROADMAP.md` automatically when it detects this was a **full** refactor cycle (Fibonacci 5+). Light path refactor work (Fibonacci 1/2/3) adds to the score as normal.
