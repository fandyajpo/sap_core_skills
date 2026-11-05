---
name: storybook-engineering
description: Documents and tests SAP Core UI components in isolated Storybook stories without duplicating business behavior.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Storybook Engineering

## Purpose
Make shared UI components discoverable, reviewable, and testable in meaningful isolated states.

## Scope
Stories, component documentation, fixtures, interaction tests, visual review, and framework configuration.

## When to Use This Skill
Use for reusable UI primitives, complex component states, and documented interaction patterns.

## When NOT to Use This Skill
Do not recreate application business logic or use stories as the only test of server authorization/workflows.

## SAP Core Context
Storybook is approved for component documentation and isolated development. It must coexist with the actual Next.js app and use its real component contracts.

## Core Principles
Stories express component inputs/states, not a second implementation of SAP Core business behavior.

## Mandatory Rules
- Cover default, loading, empty, error, disabled, permission-presentation, and edge states relevant to the component.
- Use sanitized synthetic fixtures; never use production data or secrets.
- Keep stories deterministic and independent of real database/auth services.
- Use interaction testing only where supported by the pinned Storybook version and configured runner; verify integration before relying on it.

## Architecture Guidelines
Keep stories near components or in the repository's established Storybook layout. Mock API boundaries through supported test mechanisms; do not make direct Prisma calls. Use a real visual component with accessible args/controls; avoid meaningless knobs. Share fixture builders where they reflect realistic ERP states, but keep domain invariants tested in application/domain suites.

## Implementation Guidelines
**Example:** A status badge story shows Draft, Pending Approval, Posted, and Reversed using synthetic typed props; a form story shows a server validation error and keyboard focus. It does not implement order-posting logic or fabricate an approved state transition.

## Data and Security Considerations
Prevent secrets/PII from entering static story builds and published previews. Ensure story-only routes do not expose authenticated app data. Avoid unsafe HTML fixtures except dedicated sanitized XSS tests.

## Common Anti-Patterns
Business rules reimplemented in stories; production API calls; real customer data; stories hide accessibility failures; unbounded controls; assuming visual snapshot proves authorization/security.

## Validation Requirements
**Implementation checklist:** component contract; representative states; synthetic fixture; no server-only imports; accessibility/interactions; pinned configuration. **Review checklist:** story build/test in supported environment; no secret leakage; story behavior matches app; domain logic remains tested elsewhere.

## Related Skills
react-engineering, tailwind-engineering, testing-strategy, playwright-testing, nextjs-engineering.

## Reference Documents
Storybook official docs https://storybook.js.org/docs and testing docs https://storybook.js.org/docs/writing-tests. Verify framework integration, test runner, and version compatibility with the actual Next.js setup.
