---
name: playwright-testing
description: Tests critical SAP Core user journeys and access boundaries in a real browser with Playwright.
category: Quality & Security
version: 1.0.0
status: active
---

# Playwright Testing

## Purpose
Verify user-observable behavior across browser, application routes, and backend contracts.

## Scope
E2E setup, stable locators, authentication fixtures, business journeys, browser/device behavior, and test isolation.

## When to Use This Skill
Use for critical workflows such as sign-in, approval, posting, receiving, order fulfillment, and access denial.

## When NOT to Use This Skill
Do not make every unit/domain rule a browser test or use production data/credentials.

## SAP Core Context
Playwright is approved for browser-level testing. It complements, but does not replace, PostgreSQL integration, domain, API, and CASL policy tests.

## Core Principles
Test realistic observable flows with deterministic state and explicit authorization context.

## Mandatory Rules
- Use accessible roles/names and stable test IDs only when semantic locators are insufficient.
- Isolate test data per run and use non-production accounts/credentials.
- Test both allowed and denied user/company paths at the UI and server boundary.
- Do not disable TLS/security checks in a way that diverges from production without an explicit reason.
- Keep waits condition-based; never use arbitrary sleep as the primary synchronization mechanism.

## Architecture Guidelines
Use controlled authentication setup (UI or documented setup state) appropriate to the threat/coverage goal; never reuse expiring committed storage state. Seed test data through authorized test helpers or isolated fixtures. Assert business results from the UI and authoritative API/database state where available. Keep tests independent and clean up reliably. Verify browser and Playwright version APIs before using fixtures/config.

## Implementation Guidelines
**Example:** A procurement approver signs in, selects a company, approves a specific PO revision, and sees the resulting state. A separate test signs in as another company and confirms the record is unavailable and server rejects its direct URL/API request. Tests assert accessible status text, not implementation-specific class names.

## Data and Security Considerations
Do not store production session cookies or secrets in `storageState` artifacts. Redact traces/videos/screenshots that may capture sensitive data. Protect test environment endpoints and avoid running destructive E2E flows against production.

## Common Anti-Patterns
Arbitrary sleeps; CSS selectors tied to markup; shared mutable account; tests all run against one record; UI-only security proof; checked-in auth state; output artifacts contain PII.

## Validation Requirements
**Implementation checklist:** isolated env; actor/company fixtures; stable locators; waits; cleanup; artifacts policy. **Review checklist:** run repeated/parallel to detect flake; cross-company denial; server-side rejection; no production secrets/data; assertions reflect user outcomes.

## Related Skills
testing-strategy, nextjs-engineering, authentication-engineering, authorization-engineering, storybook-engineering.

## Reference Documents
Playwright official docs https://playwright.dev/docs/intro, locators https://playwright.dev/docs/locators, authentication https://playwright.dev/docs/auth. Verify configuration and API against the pinned Playwright release.
