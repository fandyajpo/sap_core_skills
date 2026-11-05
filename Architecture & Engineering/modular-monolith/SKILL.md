---
name: modular-monolith
description: Defines enforceable SAP Core module ownership and in-process collaboration boundaries.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# Modular Monolith

## Purpose
Keep one deployable application modular through explicit business ownership and dependency rules.

## Scope
Module boundaries, internal contracts, dependency direction, shared code, and cross-module use cases.

## When to Use This Skill
Use when creating a business module, calling another domain, or moving code across boundaries.

## When NOT to Use This Skill
Do not use it to justify microservice extraction or to bypass domain-specific business rules.

## SAP Core Context
SAP Core is one deployable Next.js application with domain modules and one centrally governed PostgreSQL database. All ten schemas have owners; module boundaries are logical and must be enforced in code review and tests.

## Core Principles
A module owns its invariants and write model. In-process calls are explicit. Shared deployment does not imply shared business ownership.

## Mandatory Rules
- A module may not import another module's persistence implementation for convenience.
- Cross-module commands use a documented application contract and validate the caller's organization context.
- Avoid cycles; extract a narrow contract or redesign ownership rather than introducing reciprocal imports.

## Architecture Guidelines
Separate domain/application, infrastructure, and delivery (REST/UI) concerns according to the repository's actual conventions. Keep contracts stable and minimal. A cross-module read can use an owned query contract/read model; do not create a generic unrestricted database façade. Shared primitives must be business-neutral and versioned with their consumers.

## Implementation Guidelines
For a purchase receipt affecting inventory, procurement owns purchase-order/receipt transitions; inventory owns movement and balance invariants. Define one use-case boundary that coordinates them, its transaction scope, and failure behavior. Avoid hiding cross-module writes in ORM callbacks. Add dependency checks where the project tooling supports them; otherwise enforce imports in review.

## Data and Security Considerations
Database schemas express ownership but do not replace application boundaries. A valid foreign key does not authorize a caller. Apply tenant/company restrictions in every module query and command; audit the initiating actor and document reference.

## Common Anti-Patterns
One global service/repository; modules that mirror technical layers but mix business domains; reciprocal imports; direct cross-schema updates; “shared” entities with no owner; coupling because all code is in one deployment.

## Validation Requirements
**Example:** Procurement sends a receipt command with PO line, quantity, unit, plant, and actor context to inventory's contract; inventory validates the item/location and records movement in its own unit of work. **Implementation checklist:** owner, contract, allowed dependency, organization scope, atomicity/recovery stated. **Review checklist:** no persistence leakage, cycles, or shadow copies of domain rules; tests cover boundary rejection.

## Related Skills
system-architecture, code-organization, erp-domain-modeling, database-modeling, transaction-integrity.

## Reference Documents
SAP_CORE_CONTEXT.md, SKILL_DEPENDENCY_MAP.md, GLOBAL_RULES.md. Official architectural references: Next.js https://nextjs.org/docs; TypeScript project guidance https://www.typescriptlang.org/docs/handbook/modules.html.
