---
name: postgresql-design
description: Defines SAP Core PostgreSQL types, constraints, relationships, indexes, and schema usage.
category: Database Engineering
version: 1.0.0
status: active
---

# PostgreSQL Design

## Purpose
Model SAP Core business facts in PostgreSQL so invalid states are difficult to persist and valid workloads remain queryable.

## Scope
Relational design, ten-schema governance, types, constraints, indexes, transactions, timestamps, JSONB, and locking.

## When to Use This Skill
Use for any table, column, relation, index, constraint, SQL, or database behavior decision.

## When NOT to Use This Skill
Do not rely on database design alone to replace application-domain validation or authorization.

## SAP Core Context
Prisma Postgres is the managed platform; PostgreSQL is the engine. The single `sap_core` database contains ten owned schemas. Prisma ORM and Prisma Migrate are the normal app access/evolution tools.

## Core Principles
Store facts with explicit types and constraints. Normalize authoritative relational data; optimize only measured access paths.

## Mandatory Rules
- Use UUID identifiers as baseline; keep human document numbers as separately scoped business identifiers.
- Use `numeric`/exact decimal semantics for money and precision-sensitive quantities; define precision, scale, currency/unit, and rounding explicitly.
- Use foreign keys, unique/check constraints, and `NOT NULL` where they express durable invariants. PostgreSQL `CHECK` passes when the expression is true or NULL, so pair it with `NOT NULL` where absence is invalid; keep `CHECK` row-local and enforce cross-row invariants with appropriate relational constraints or transaction/concurrency protocols.
- Define null semantics for unique keys: PostgreSQL treats NULLs as distinct by default, so required key components should be `NOT NULL` or use an explicitly selected, version-supported alternative.
- Distinguish date-only business values from instants; use timezone-aware timestamp semantics for instants and explicitly defined business timezone rules.
- Add indexes for measured/expected query predicates and joins; include uniqueness constraints where required. PostgreSQL does not automatically index referencing foreign-key columns; add such indexes when referential checks or workload justify them.

## Architecture Guidelines
Place tables in the owning schema from SAP_CORE_CONTEXT.md. Name tables/columns consistently with the repository's chosen convention; use explicit `@@schema` mapping only after confirming Prisma's pinned multi-schema support. Define FK delete/update behavior deliberately; financial/stock facts should not cascade-delete. Prefer composite indexes aligned with filters and ordering, and partial indexes only when predicate selectivity and lifecycle semantics justify them. JSONB is for genuinely variable payloads, not core relationships or attributes needing constraints/reporting.

## Implementation Guidelines
**Example:** A stock movement stores product/material, location, signed quantity in an identified unit, posting time, source document, and actor. A balance row is a transactionally maintained projection, not the sole stock history. A uniqueness constraint can protect one balance per required company/plant/location/material/unit key. Measure query plans before adding overlapping indexes.

## Data and Security Considerations
Database privileges should follow least privilege. Avoid broad database roles in application paths. Do not expose connection strings or raw SQL errors. Row-level security is not assumed as a substitute for app authorization; if introduced, test connection/session context safely with pooling.

## Common Anti-Patterns
Money in float/double; dates stored as ambiguous text; cascade deletion of posted facts; JSON blobs replacing relational structure; indexes on every column; relying on default timezone; constraints only in UI; `MAX()+1` numbering.

## Validation Requirements
**Implementation checklist:** owner/schema; keys/FKs/delete policy; nullability/unique/check constraints; exact numeric and time semantics; query-aligned indexes; migration and data-volume impact. **Review checklist:** invalid states rejected under concurrent/alternate writes; plans checked for critical queries; no secrets or sensitive data in schema diagnostics.

## Related Skills
database-modeling, database-integrity, prisma-orm, database-performance, database-security, date-time-engineering.

## Reference Documents
PostgreSQL official docs: constraints https://www.postgresql.org/docs/current/ddl-constraints.html; numeric/date types https://www.postgresql.org/docs/current/datatype-numeric.html and https://www.postgresql.org/docs/current/datatype-datetime.html; indexes https://www.postgresql.org/docs/current/indexes.html; locking https://www.postgresql.org/docs/current/explicit-locking.html. Use docs for the deployed PostgreSQL major version.
