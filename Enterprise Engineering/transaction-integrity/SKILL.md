---
name: transaction-integrity
description: Defines atomic, retry-safe SAP Core business operations across related records.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Transaction Integrity

## Purpose
Ensure one business action either records its required facts consistently or leaves no misleading partial result.

## Scope
Transaction boundaries, multi-row workflows, posting, idempotency, failure behavior, and consistency across module contracts.

## When to Use This Skill
Use for any financial posting, stock change, approval transition, number allocation, or multi-record lifecycle command.

## When NOT to Use This Skill
Do not use a database transaction as a substitute for distributed transactions or to hold locks while calling external services.

## SAP Core Context
The modular monolith and one PostgreSQL database enable local transactional operations. External services are deferred; cross-module coordination must be explicit and bounded.

## Core Principles
Atomicity is about one defined business invariant. Transaction boundaries should be as small as possible while containing all records necessary to uphold it.

## Mandatory Rules
- Identify the invariant and all writes that must commit together before starting implementation.
- Keep transactions short; perform validation/preparation before locking where safe; no network calls or user interaction inside.
- Make retries safe with an idempotency key, unique business constraint, or state/version guard.
- Do not report success before commit; map rollback/conflict to a safe retryable or domain error.
- Use a concurrency protocol for shared mutable values; transaction alone does not prevent all races.

## Architecture Guidelines
A local workflow coordinator may orchestrate module-owned operations, but ownership remains clear. When one transaction cannot cover all operations, define durable intermediate states, retry/recovery, and compensation explicitly rather than pretending atomicity. Use isolation level/locks only after understanding PostgreSQL behavior and the Prisma transaction API for the pinned version. Application invariants that span rows need serialization or a database constraint/protocol.

## Implementation Guidelines
**Example:** A journal posting transaction inserts the immutable header/lines and updates posting state only after checking exact balance and open period. The operation uses a unique request/document version to prevent duplicate posting. A stock transfer transaction adjusts both balance projections and inserts both linked movements; if either side fails, rollback leaves neither side applied.

## Data and Security Considerations
Authorization and company scope are checked inside the same logical operation and revalidated against mutable state. Avoid leaking lock/SQL details. Record audit facts in the transaction when the audit event is required to prove the business change; do not log credentials.

## Common Anti-Patterns
Several independent writes with no rollback; long transaction; retrying unknown commit outcome as a new command; assuming default isolation prevents write skew; external call while holding a row lock; transaction too broad across unrelated aggregates.

## Validation Requirements
**Implementation checklist:** invariant; write set; transaction API/version; idempotency; concurrency protocol; timeout/failure behavior; audit; reconciliation. **Review checklist:** fault injection/rollback; duplicate/retry; concurrent conflicting command; post-commit response semantics; no network wait inside transaction.

## Related Skills
database-integrity, concurrency-control, erp-domain-modeling, prisma-orm, inventory-management, financial-accounting.

## Reference Documents
PostgreSQL transaction isolation https://www.postgresql.org/docs/current/transaction-iso.html and explicit locking https://www.postgresql.org/docs/current/explicit-locking.html; Prisma transactions https://www.prisma.io/docs/orm/prisma-client/queries/transactions. Confirm exact API and isolation support against pinned Prisma/PostgreSQL versions.
