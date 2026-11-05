---
name: database-modeling
description: Assigns ownership, relationships, lifecycle, and schema placement to SAP Core data.
category: Database Engineering
version: 1.0.0
status: active
---

# Database Modeling

## Purpose
Turn ERP concepts into owned, relational entities with defensible lifecycle and integrity rules.

## Scope
Entity boundaries, schema placement, relationships, identifiers, audit/soft-delete policy, and naming.

## When to Use This Skill
Use before adding or materially changing a table, relationship, or persisted state.

## When NOT to Use This Skill
Do not add tables to mimic every screen or create generic schemas without business ownership.

## SAP Core Context
The ten schemas and initial entity references are governed by SAP_CORE_CONTEXT.md. Each table has one owning module even when other domains reference it.

## Core Principles
Model business facts and lifecycle, not UI shape. A foreign-key relationship does not transfer write ownership.

## Mandatory Rules
- Name owner, source of truth, lifecycle, cardinality, and invariants before modeling.
- Use stable UUID IDs by default; keep scoped human document numbers separate.
- Define created/updated actor/time, audit, retention, and deletion rules intentionally.
- Do not create cross-module duplicate master records to avoid a contract.

## Architecture Guidelines
Separate master data from transactional snapshots when a document must preserve the terms as agreed at posting/order time (for example, address, unit/price, tax context). Decide whether a reference is immutable historical evidence or a live relation. Use join tables for real many-to-many relationships with attributes. Choose FK behavior deliberately; posted records generally restrict deletion and reference historical identifiers. Use `system` only for genuinely cross-cutting platform records.

## Implementation Guidelines
**Example:** `sales.sales_orders` references `master_data.customers` by the stable ID but stores an order-time customer/address/price snapshot required for historical document rendering. Sales owns its order fields; master-data owns the customer record. Add a new entity only when the lifecycle/meaning cannot be represented cleanly by an existing owner.

## Data and Security Considerations
Company/organization scope is explicit in the model or inherited through a rigorously defined parent; every read/write still checks scope. Avoid soft deletion when it hides active references or violates ledgers. Sensitive HR attributes need restricted access and retention policy.

## Common Anti-Patterns
One generic `documents` table for unrelated aggregates; duplicate live customer/vendor data in modules; ambiguous polymorphic foreign keys; nullable columns encoding multiple entities; soft-delete as an audit substitute; schema chosen by convenience.

## Validation Requirements
**Implementation checklist:** owner/schema; lifecycle and legal transitions; key and references; snapshot/live semantics; constraints; audit/deletion/retention; access scope; migration plan. **Review checklist:** no duplicate source of truth; relational reports feasible; FK behavior safe; ownership communicated to dependent modules.

## Related Skills
erp-domain-modeling, postgresql-design, modular-monolith, database-integrity, core-organization.

## Reference Documents
SAP_CORE_CONTEXT.md schema registry; PostgreSQL constraints https://www.postgresql.org/docs/current/ddl-constraints.html; Prisma data modeling https://www.prisma.io/docs/orm/prisma-schema/data-model.
