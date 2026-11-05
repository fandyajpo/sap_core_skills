---
name: system-architecture
description: Governs SAP Core's system-wide boundaries, runtime responsibilities, and architectural change.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# System Architecture

## Purpose
Keep SAP Core coherent as a secure, database-first ERP platform.

## Scope
Product-wide architecture, deployable shape, technology ownership, decision records, and tradeoffs.

## When to Use This Skill
Use for cross-cutting changes, new platform capabilities, technology decisions, or changes affecting several modules.

## When NOT to Use This Skill
Do not use this to design a single query or component in isolation; use the owning domain skill and its dependencies.

## SAP Core Context
Initial architecture is one Next.js modular monolith, one Prisma Postgres-managed PostgreSQL database (`sap_core`), and ten governed schemas. External service integrations are deferred.

## Core Principles
Optimize for explicit ownership, relational integrity, operational simplicity, and reversible evolution—not speculative scale.

## Mandatory Rules
- Record material architecture decisions and supersession in DECISION_LOG.md.
- Distinguish platform, engine, ORM, and migration tool.
- Do not add a database, broker, microservice, or replacement library without a demonstrated requirement and approved decision.

## Architecture Guidelines
Assign every capability an owning module, API boundary, data owner, and allowed dependency direction. Keep framework concerns at the application edge; domain use cases remain testable without browser state. Cross-module workflows call explicit application contracts and share a transaction only through an intentionally designed boundary.

## Implementation Guidelines
Before choosing a pattern, map actors, lifecycle, tables/schemas, failure cases, consistency needs, and deployment rollout. Prefer a local transaction over distributed coordination. Where framework/provider compatibility is uncertain, verify the exact pinned version and document the evidence before adoption.

## Data and Security Considerations
Organization scope, server-side authorization, database constraints, and audit needs are architectural properties, not later hardening. Never expose Prisma Client or credentials to the browser. Treat cache invalidation and audit evidence as part of the design.

## Common Anti-Patterns
Premature microservices; generic “common” modules that own business rules; duplicated authorization; direct table coupling; assuming Vite+ replaces Next.js; choosing technology because it is familiar rather than justified.

## Validation Requirements
**Design example:** A sales fulfillment flow requests a stock reservation through the inventory application contract; sales does not update `inventory.stock_balances` directly. **Implementation checklist:** owner, contract, failure/transaction boundary, auth scope, migration and rollout identified. **Review checklist:** no new infra without evidence; no cross-module persistence import; decision log/context/index updated where needed.

## Related Skills
modular-monolith, code-organization, database-modeling, api-engineering, release-management.

## Reference Documents
SAP_CORE_CONTEXT.md; DECISION_LOG.md; GLOBAL_RULES.md. Official references: Next.js https://nextjs.org/docs, PostgreSQL https://www.postgresql.org/docs/, Prisma https://www.prisma.io/docs. Check pinned versions before relying on specific behavior.
