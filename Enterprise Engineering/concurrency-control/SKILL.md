---
name: concurrency-control
description: Prevents lost updates, over-allocation, duplicate actions, and inconsistent numbering in SAP Core.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Concurrency Control

## Purpose
Make shared business state correct when requests execute simultaneously or are retried.

## Scope
Pessimistic locks, optimistic versions, uniqueness, atomic updates, isolation, and retry policy.

## When to Use This Skill
Use for stock, document numbers, approvals, balances, master-data duplicates, and any read-modify-write operation.

## When NOT to Use This Skill
Do not add a distributed lock or external coordination service when PostgreSQL can safely own the local invariant.

## SAP Core Context
PostgreSQL is the shared engine for the modular monolith. Stock movements, accounting posts, approvals, and number ranges require explicit concurrency behavior.

## Core Principles
Choose a protocol matching the invariant: unique constraint, atomic conditional write, optimistic version check, row lock, or serializable transaction with safe retry.

## Mandatory Rules
- Never implement `read value → calculate → unconditional update` for contested state.
- Use a database uniqueness constraint as the final guard against duplicate business keys.
- When lock/isolation errors are retryable, retry only a bounded operation with idempotency and a clearly classified error.
- Treat deadlocks/serialization failures as expected failure modes; avoid lock-order inconsistency.

## Architecture Guidelines
For stock, lock rows in deterministic order or perform conditional updates that verify sufficient quantity and affected-row count. For approvals, compare document version/state in the update and ensure the actor isn't deciding against a changed version. For numbering, serialize allocation per scope using a counter row/sequence plus uniqueness policy. PostgreSQL row-level locks block conflicting writers/lockers until transaction end; ordinary readers are generally not blocked by row locks. Do not rely on uncommitted process memory.

## Implementation Guidelines
**Example:** Two orders attempt to reserve the final five units. One transaction obtains the stock row lock, verifies availability, records reservation/movement and updates the balance; the other sees the new committed value and fails or reserves only remaining quantity. Both cannot succeed based on a stale read.

## Data and Security Considerations
A lock is not authorization; scope rows by company/plant. Keep lock duration small. Avoid returning sensitive contention details. Review pooled connection behavior for transaction-scoped settings and never assume a session-level variable is isolated between requests.

## Common Anti-Patterns
`MAX()+1`; process mutex for multi-instance app; read-then-write without version; unbounded retry; inconsistent lock order; serializable mode without retry handling; using optimistic concurrency but ignoring affected-row count.

## Validation Requirements
**Implementation checklist:** invariant and conflict scope; selected protocol; unique constraint; lock ordering/version; retry and idempotency; timeout. **Review checklist:** synchronized concurrent test; duplicate/last-item scenario; deadlock/serialization handling; no lost update; lock released on rollback.

## Related Skills
transaction-integrity, postgresql-design, database-integrity, inventory-management, document-numbering, workflow-engine.

## Reference Documents
PostgreSQL explicit locking https://www.postgresql.org/docs/current/explicit-locking.html and transaction isolation https://www.postgresql.org/docs/current/transaction-iso.html. Prisma transaction API is version-sensitive: https://www.prisma.io/docs/orm/prisma-client/queries/transactions.
