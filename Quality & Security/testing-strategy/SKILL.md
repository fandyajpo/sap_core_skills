---
name: testing-strategy
description: Defines risk-based tests for SAP Core business behavior, persistence, security, and browser workflows.
category: Quality & Security
version: 1.0.0
status: active
---

# Testing Strategy

## Purpose
Provide evidence that business invariants and failure behavior hold at the layer where they can fail.

## Scope
Unit/domain, application, database integration, API, component, browser, migration, and regression tests.

## When to Use This Skill
Use for all new behavior, defect fixes, schema changes, security policies, and architecture boundary changes.

## When NOT to Use This Skill
Do not substitute snapshots or broad mocks for tests of financial, inventory, authorization, or database invariants.

## SAP Core Context
Approved testing tools include Playwright and Storybook; use the repository's existing runner for other layers. The actual test/build commands depend on the application repo and are not prescribed here.

## Core Principles
Test outcomes and invariants, not implementation trivia. Use real PostgreSQL semantics for transaction/constraint behavior where practical.

## Mandatory Rules
- Test domain state transitions and exact financial arithmetic without relying on UI validation.
- Include server-side authorization and cross-company denial tests for protected paths.
- Test DB constraints, rollback, idempotency, and concurrency with integration-level infrastructure when the behavior depends on PostgreSQL.
- Keep tests deterministic, isolated, and free of production secrets/data.
- Test migration from prior schema and fresh setup when a migration changes persisted structure.

## Architecture Guidelines
Use a testing pyramid with fast unit/domain tests, focused application/service tests, real database integration for persistence semantics, contract/API tests, and Playwright for critical user journeys. Mock external dependencies at module boundaries; external integrations are deferred. Avoid overmocking Prisma when correctness depends on constraints or isolation. Storybook interaction tests cover component behavior, not authorization.

## Implementation Guidelines
**Example:** Journal posting tests cover balanced success, imbalance rejection, closed period, unauthorized account/company, duplicate retry, transaction rollback, and immutable reversal. A PostgreSQL integration test exercises unique constraint/concurrent posting; a Playwright journey verifies a permitted user sees safe feedback but does not replace API-level denial tests.

## Data and Security Considerations
Use synthetic tenant-scoped fixtures and isolated test databases. Never use production PII. Avoid checking in secrets or reusing live auth tokens. Ensure test logs do not expose sensitive fixture data.

## Common Anti-Patterns
Only happy-path tests; mocks that make transactions always succeed; flaky time/network tests; test data shared across cases; browser tests used for all rules; skipped test reported as passed; no migration upgrade path.

## Validation Requirements
**Implementation checklist:** test layers mapped to risks; negative/race/retry cases; fixture isolation; actual DB for DB invariants; migration checks; actual test runner commands found. **Review checklist:** tests fail when invariant is broken; no secret/live data; deterministic cleanup; report precisely what ran.

## Related Skills
playwright-testing, storybook-engineering, prisma-migrate, code-review, release-management.

## Reference Documents
Playwright https://playwright.dev/docs/intro; Storybook testing https://storybook.js.org/docs/writing-tests; PostgreSQL isolation https://www.postgresql.org/docs/current/transaction-iso.html. Follow actual repository tooling.
