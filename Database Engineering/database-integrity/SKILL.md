---
name: database-integrity
description: Protects SAP Core business invariants at persistence and transaction boundaries.
category: Database Engineering
version: 1.0.0
status: active
---

# Database Integrity

## Purpose
Ensure persisted records remain internally valid under retries, concurrency, failures, and multiple application paths.

## Scope
Constraints, atomic writes, referential behavior, idempotency, reconciliation, and invariant placement.

## When to Use This Skill
Use for related-row writes, lifecycle changes, postings, stock updates, or any invariant with concurrent callers.

## When NOT to Use This Skill
Do not rely solely on the database for user-facing explanations or domain authorization.

## SAP Core Context
One PostgreSQL database backs a modular monolith. Module ownership remains enforced even when database constraints connect schemas. Finance and inventory require especially strong immutable/auditable facts.

## Core Principles
Enforce an invariant at the lowest reliable layer that can express it, and also make application behavior understandable. Atomicity means all required facts commit or none do.

## Mandatory Rules
- Use DB constraints for uniqueness, required relations, and row-local checks where supported by the model.
- Use a transaction for multi-row business facts; define transaction scope explicitly.
- Never swallow constraint or transaction failures and report success.
- Build reconciliation paths for derived balances and denormalized summaries.

## Architecture Guidelines
Cross-row rules (balanced journal totals, available stock) may require a controlled application transaction and concurrency protocol in addition to database constraints. Keep operations bounded and deterministic; make external calls outside transaction boundaries. Use idempotency keys or unique business keys for retried commands. Set delete behavior to protect posted history. For cross-module operations, define one coordinator and explicit ownership of each write.

## Implementation Guidelines
**Example:** Posting journal entry and lines: validate allowed period/accounts, compute debit/credit using exact decimal arithmetic, persist header/lines and posting state in one transaction, and prevent duplicate posting with a state/version or idempotency key. A reconciliation query compares ledger totals and balance projections; discrepancies are surfaced, not silently overwritten.

## Data and Security Considerations
Constraints do not authorize the caller. Check actor and organization scope before writes. Do not put secrets in constraints or error strings. Avoid transaction retries that repeat non-idempotent effects.

## Common Anti-Patterns
Application-only uniqueness; partial writes on caught errors; long transaction with network wait; automatic cascade of ledger rows; computed totals trusted without recomputation; retrying a committed operation as new; integrity rules that differ by endpoint.

## Validation Requirements
**Implementation checklist:** invariant classified (row, cross-row, concurrent); enforcement layer chosen; transaction and idempotency defined; rollback behavior tested; reconciliation path exists. **Review checklist:** concurrent duplicate/invalid attempts tested; constraints survive direct data path; failures are visible; no posted history mutation.

## Related Skills
postgresql-design, prisma-orm, transaction-integrity, concurrency-control, database-modeling.

## Reference Documents
PostgreSQL constraints https://www.postgresql.org/docs/current/ddl-constraints.html; transactions https://www.postgresql.org/docs/current/tutorial-transactions.html; Prisma transactions https://www.prisma.io/docs/orm/prisma-client/queries/transactions. Verify the transaction API for the pinned Prisma version.
