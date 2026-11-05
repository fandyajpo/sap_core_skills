---
name: prisma-orm
description: Governs server-side Prisma ORM modeling and access for SAP Core domain modules.
category: Database Engineering
version: 1.0.0
status: active
---

# Prisma ORM

## Purpose
Provide a consistent typed server-side data-access path while retaining PostgreSQL integrity and module ownership.

## Scope
Prisma Schema, generated client usage, queries, transactions, raw SQL exceptions, and schema mapping.

## When to Use This Skill
Use when changing Prisma models, queries, client lifecycle, relation access, or database interaction.

## When NOT to Use This Skill
Do not use the client in browser code, as a replacement for domain validation, or as a reason to bypass module boundaries.

## SAP Core Context
Prisma ORM is distinct from Prisma Postgres (managed platform), PostgreSQL (engine), and Prisma Migrate (migration system). The one database uses ten logical schemas; exact multi-schema setup must match the selected Prisma version/provider.

## Core Principles
Repositories/adapters translate persistence; use cases own business decisions. Database constraints remain authoritative for durable invariants.

## Mandatory Rules
- Keep Prisma Client in server-only infrastructure and initialize it according to the actual runtime/deployment model.
- Map each model to its governed schema using the pinned version's documented capabilities; the current documentation describes `schemas` in the datasource and model `@@schema`, but verify required syntax/feature availability.
- Select only needed fields and return module-owned DTOs, not raw client objects.
- Use transactions for atomic business facts; keep them bounded and free of network calls.
- Use parameterized raw SQL only when justified; document why Prisma's supported query API is insufficient and test it against PostgreSQL.

## Architecture Guidelines
Keep one governed client/configuration per server runtime as appropriate to the repository; do not create ad hoc clients per request. Module adapters should expose intent-bearing methods, not a global generic CRUD API. Avoid leaking Prisma model types into browser contracts. Treat generated client output as build output, not manually edited source.

## Implementation Guidelines
**Example:** `postJournalEntry(command)` first validates the domain command and actor/company, then a transaction creates the immutable entry and lines and enforces balanced exact-decimal totals before commit; database constraints protect uniqueness/relations. On conflict, return a domain-safe result. Do not expose an endpoint that accepts an arbitrary Prisma `create` object.

## Data and Security Considerations
Scope queries to authorized company/organization. Avoid mass assignment and unrestricted `include` graphs. Handle unique/FK/serialization errors without leaking SQL. Connection pooling and transaction behavior depend on Prisma Postgres connection mode, adapter, and deployment; verify with official provider docs.

## Common Anti-Patterns
Client bundle imports; one repository for all schemas; arbitrary CRUD; returning generated model values; nested writes used without lifecycle validation; swallowing constraint errors; unreviewed raw SQL; assumptions about pooled connections.

## Validation Requirements
**Implementation checklist:** pinned Prisma/provider checked; schema mapping correct; least-data query; owner boundary; input and authorization; transaction/error semantics; generated client refreshed through documented workflow. **Review checklist:** typecheck; query count/plan for hot paths; transaction rollback/duplicate cases; no client exposure; raw SQL justified and parameterized.

## Related Skills
postgresql-design, prisma-migrate, database-integrity, database-security, modular-monolith, transaction-integrity.

## Reference Documents
Prisma official docs https://www.prisma.io/docs/orm and multi-schema guide https://www.prisma.io/docs/orm/prisma-schema/data-model/multi-schema. Prisma's current documentation surfaced `schemas` and `@@schema`; multi-schema was reported GA for Prisma ORM 6.13.0 in official material, but verify support, provider, and syntax for the pinned release before use.
