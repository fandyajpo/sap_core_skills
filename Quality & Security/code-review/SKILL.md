---
name: code-review
description: Reviews SAP Core changes against module boundaries, security, data integrity, and operational safety.
category: Quality & Security
version: 1.0.0
status: active
---

# Code Review

## Purpose
Catch defects and architectural drift before changes are merged or released.

## Scope
Behavior, module ownership, database/migrations, security, tests, UX/accessibility, and operability.

## When to Use This Skill
Use for every code, schema, dependency, configuration, or skill-document change.

## When NOT to Use This Skill
Do not approve by style preference alone or claim all risks are resolved when checks were not run.

## SAP Core Context
GLOBAL_RULES.md and the affected skills are review criteria. The product is a modular monolith with ten schema owners and deferred integrations.

## Core Principles
Review for correctness, risk, and maintainability. Understand the request and data effects before line-by-line comments.

## Mandatory Rules
- Verify the owning module/schema and direct dependency direction.
- Review authorization at server operation boundaries and company/resource scoping.
- Review transactions, concurrency, idempotency, immutable posted facts, exact decimals, and migration rollout where applicable.
- Check that every validation claim is backed by an actually run check; do not infer success from code presence.
- Flag secret leakage, raw SQL, new dependencies, external calls, and unsupported version assumptions.

## Architecture Guidelines
Prioritize blocking defects: data loss/corruption, privilege escalation/cross-company exposure, migration hazards, financial/stock imbalance, then behavior and maintainability. Distinguish required changes from questions/nits. Check callers, tests, generated artifacts, config and links. For cross-module code, inspect imports and persistence access, not only filenames.

## Implementation Guidelines
**Example:** Reviewing a new stock issue, trace API → actor context → CASL → inventory use case → transaction/lock → movement+balance → retry/error → tests/reconciliation. Reject a review that tests only a successful single-user click while concurrent oversell remains possible.

## Data and Security Considerations
Look for IDOR, mass assignment, unsafe outputs, cache leaks, secret logging, broad database roles, and new file/parser attack surfaces. Ensure audit changes don't persist sensitive payloads unnecessarily.

## Common Anti-Patterns
Reviewing only formatting; nitpicking without severity; approving with unrun tests; checking UI authorization only; ignoring migration history; direct cross-module repository imports; rubber-stamping AI-generated code.

## Validation Requirements
**Implementation checklist:** request/acceptance criteria; affected skills; migration and tests inspected; risk-ranked findings. **Review checklist:** required checks recorded; owners and dependencies; security/data invariant; negative/concurrent cases; version APIs verified; remaining risk clearly stated.

## Related Skills
GLOBAL_RULES.md; `testing-strategy`, `application-security`, `prisma-migrate`, `transaction-integrity`, and affected domain skills.

## Reference Documents
DEVELOPMENT_WORKFLOW.md; GLOBAL_RULES.md; OWASP ASVS https://owasp.org/www-project-application-security-verification-standard/. Use repository-specific CI/tooling and do not claim execution that did not occur.
