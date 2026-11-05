---
name: database-performance
description: Measures and improves PostgreSQL and Prisma workload performance without weakening correctness.
category: Database Engineering
version: 1.0.0
status: active
---

# Database Performance

## Purpose
Keep ERP queries and transactions responsive through workload evidence and safe tuning.

## Scope
Query shape, indexes, plans, pagination, connection use, row volume, and data growth.

## When to Use This Skill
Use for slow endpoints, growing tables, high-contention writes, or before adding substantial reporting queries.

## When NOT to Use This Skill
Do not add speculative indexes or remove integrity constraints to hide an unmeasured bottleneck.

## SAP Core Context
ERP tables can grow for years. Prisma is the access layer and PostgreSQL the engine; performance changes must preserve company scope, history, and module ownership.

## Core Principles
Measure representative workloads, optimize the dominant cost, and compare behavior before/after.

## Mandatory Rules
- Never fetch an unbounded ERP dataset into the browser.
- Select only needed columns and paginate at the server boundary.
- Use query plans and realistic data distributions to justify indexes.
- Keep financial/inventory correctness constraints even if they add write cost.

## Architecture Guidelines
Use indexes aligned to common scoped predicates, joins, and orderings; consider composite column order and selectivity. Avoid redundant indexes that increase write/storage cost. Use keyset pagination for deep/changing lists when the contract supports stable ordering; use offset only with bounded pages and clear consistency expectations. Batch operations within safe limits. Report queries use explicit aggregates and authorization filters; avoid N+1 relation access. Connection pool settings and transaction contention must be evaluated for actual Prisma Postgres topology.

## Implementation Guidelines
**Example:** For purchase orders filtered by company and status and ordered by creation time, inspect the actual query plan and data distribution; a composite index may be justified if it matches the access path. Return only requested page and columns. Do not assume that a matching-looking index helps without `EXPLAIN` evidence.

## Data and Security Considerations
Optimization must not remove tenant predicates or expose records through cached aggregate results. Avoid logging full query parameters that contain sensitive values. Control report export size and duration.

## Common Anti-Patterns
Indexing every field; `SELECT *`; N+1 queries; large offset scans; fetching all rows for client filtering; premature caching; disabling constraints; running `EXPLAIN ANALYZE` on mutating SQL in production without safety review.

## Validation Requirements
**Implementation checklist:** baseline latency/volume recorded; plan and representative dataset; indexes justified; page and query limits; write amplification assessed. **Review checklist:** correctness and company scoping preserved; before/after latency and rows/bytes measured; no hidden full scan or long transaction; rollback of tuning available.

## Related Skills
postgresql-design, prisma-orm, performance-optimization, tanstack-table, tanstack-virtual.

## Reference Documents
PostgreSQL EXPLAIN https://www.postgresql.org/docs/current/using-explain.html and indexes https://www.postgresql.org/docs/current/indexes.html; Prisma query optimization https://www.prisma.io/docs/orm/prisma-client/queries/advanced/query-optimization. Confirm behavior with the deployed engine and pinned client.
