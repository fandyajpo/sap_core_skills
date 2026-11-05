---
name: code-organization
description: Organizes SAP Core code around module ownership and dependency direction.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# Code Organization

## Purpose
Make ownership and allowed dependencies visible in the repository layout.

## Scope
Feature/module layout, naming, exports, test placement, and separation of delivery/application/domain/infrastructure concerns.

## When to Use This Skill
Use when adding files, choosing a package/folder, moving code, or reviewing a dependency path.

## When NOT to Use This Skill
Do not impose a new directory convention without inspecting the existing repository; do not reorganize unrelated code.

## SAP Core Context
The product is a modular monolith. Layout should reflect business ownership, while shared infrastructure remains explicitly governed. Follow the application's actual framework conventions after inspecting it.

## Core Principles
A reader should be able to identify the owning module and direction of dependencies from imports and file placement.

## Mandatory Rules
- Place business rules with the domain/application owner, not in generic utilities or route components.
- Keep Prisma infrastructure server-side and behind module-owned data access.
- Avoid cycles, broad barrel exports, and hidden side effects on import.

## Architecture Guidelines
Organize by business capability first where the existing codebase supports it; keep each module's domain/application/persistence/delivery concerns distinguishable. Shared packages expose small stable contracts, not all internals. Keep tests near the behavior or in the established test layout. Do not create abstractions until at least one concrete use case justifies their semantics.

## Implementation Guidelines
**Example:** Put order posting rules inside sales application/domain code and its persistence adapter inside the sales-owned server area; a route imports the sales command contract, not its Prisma repository. A reusable currency formatter may be shared UI code; posting arithmetic is not a generic utility.

## Data and Security Considerations
Use server-only boundaries for credentials and ORM code. File placement alone is not access control, so enforce authorization in the use case. Avoid shared DTOs that include sensitive columns by default.

## Common Anti-Patterns
Folders by file extension only; a catch-all `utils`; global repository for all schemas; deep imports into another module; cyclic barrels; premature package splitting; moving files without updating all imports/tests.

## Validation Requirements
**Implementation checklist:** owner/path chosen; dependency direction checked; exports narrow; tests co-located by convention; server-only code guarded per project. **Review checklist:** inspect imports/cycles and generated/client boundaries; verify no cross-module persistence access; ensure links and callers updated after moves.

## Related Skills
modular-monolith, system-architecture, typescript-engineering, nextjs-engineering.

## Reference Documents
Use the repository's existing tree and conventions first. Official references: TypeScript modules https://www.typescriptlang.org/docs/handbook/modules.html; Next.js project structure https://nextjs.org/docs/app/getting-started/project-structure.
