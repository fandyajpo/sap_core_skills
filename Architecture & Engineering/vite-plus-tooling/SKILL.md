---
name: vite-plus-tooling
description: Constrains Vite+ to verified development-tooling tasks alongside the Next.js application.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# Vite+ Tooling

## Purpose
Evaluate Vite+ as an approved development toolchain without assuming it replaces Next.js.

## Scope
Task ownership, compatibility investigation, scripts, and boundaries between Vite+ and framework-native workflows.

## When to Use This Skill
Use when configuring Vite+, adding a Vite+-managed task, or evaluating a claim that it can build/test the app.

## When NOT to Use This Skill
Do not use it to change production compilation, routing, rendering, or deployment unless compatibility is demonstrated and approved.

## SAP Core Context
Vite+ is listed as a development toolchain; Next.js remains the application framework. No specific Vite+ feature/version or compatibility matrix is prescribed.

## Core Principles
Compatibility is a tested fact for pinned versions, not an assumption based on shared JavaScript tooling.

## Mandatory Rules
- Do not replace Next.js commands or generated output on the basis of Vite/Vite+ general capabilities.
- Verify official Vite+ documentation and Next.js compatibility for the exact version, task, plugins, and deployment target.
- Keep one authoritative owner for each build/test task; document the source of truth.

## Architecture Guidelines
Potentially safe ownership may include isolated lint/format or non-framework tooling only after the actual Vite+ feature is identified and verified. Next.js-specific routing, rendering, server/client bundling, and production output remain under Next.js-native supported workflows unless a documented compatibility test proves otherwise. Do not create duplicate conflicting scripts.

## Implementation Guidelines
**Example:** Before delegating a unit-test task, establish whether the selected Vite+ release supports the project's test setup and Next.js module aliases; run a minimal representative test in CI, compare environment behavior, and record the result. If not established, retain the existing supported test runner/tooling rather than inventing configuration.

## Data and Security Considerations
Tooling must not expose `.env` values in logs or commit generated secret files. Build/test tools can execute code and must use least-privileged CI credentials. Keep client bundle analysis separate from server-only secrets.

## Common Anti-Patterns
Treating Vite and Next.js as interchangeable; assuming Vite+ compatibility from marketing; maintaining duplicate build pipelines; changing production output without deployment validation; claiming a feature based on an unverified API.

## Validation Requirements
**Implementation checklist:** exact Vite+ version and documented feature identified; compatibility with pinned Next.js verified; task ownership unambiguous; representative CI check exists. **Review checklist:** no accidental routing/compiler migration, no duplicate authority, docs/decision updated, failure mode and rollback known.

## Related Skills
nextjs-engineering, testing-strategy, release-management, system-architecture.

## Reference Documents
Vite+ official site/docs: https://viteplus.dev/ ; Next.js official docs: https://nextjs.org/docs. **Compatibility status:** not established by this skill; verify exact versions and workflow before adoption.
