---
name: inventory-management
description: Preserves traceable, transaction-safe SAP Core stock movement and balance behavior.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Inventory Management

## Purpose
Represent stock custody and movement with an auditable ledger and reconcilable balances.

## Scope
Storage locations, stock balances, movements, transfers, receipts, issues, adjustments, and valuation concepts.

## When to Use This Skill
Use for any operation that receives, reserves, transfers, consumes, ships, adjusts, or reports physical stock.

## When NOT to Use This Skill
Do not represent stock as a directly editable quantity with no movement history; do not implement an external warehouse integration in the foundation.

## SAP Core Context
Inventory owns `inventory.storage_locations`, `stock_balances`, `stock_movements`, and `stock_transfers`. Procurement, sales, and production coordinate through contracts; the integration schema remains reserved.

## Core Principles
Movement rows are the traceable facts; balance rows are transactionally maintained projections. Every quantity has an item, stock scope, unit, direction, source, actor, and posting time.

## Mandatory Rules
- Create movements for all posted stock changes; never update a balance as an isolated business action.
- Update movement and balance atomically, with a concurrency strategy preventing lost updates/overselling.
- Define negative-stock policy, reservation semantics, unit conversion, lot/serial/quality dimensions, and valuation scope before implementation.
- Make transfers traceable from source to destination and distinguish in-transit stock when workflow requires it.
- Provide reconciliation between movement history and balances.

## Architecture Guidelines
Use a unique balance key for the exact stock dimensions (company/plant/location/item/unit and any lot/status dimensions actually required). Lock or use an optimistic/versioned conditional update for contested balances; retry only on known transient conflicts and make requests idempotent. Keep movement history append-oriented. Financial valuation is a finance/accounting concern coordinated by explicit contracts, not an arbitrary inventory table update.

## Implementation Guidelines
**Example:** A 12-unit transfer is one use case: validate source available quantity, record issue-from-source and receipt-to-destination (or a staged in-transit state), update both balances in one bounded transaction, and link both movements to one transfer ID. If any step fails, no partial transfer appears. Two concurrent issues cannot both consume the same last units.

## Data and Security Considerations
Authorize company/plant/location scope server-side. Audit adjustments with reason and actor. Preserve exact quantity precision and UOM conversion evidence. Restrict deletion of movements; corrections use compensating movements. Avoid logging sensitive lot/customer data unnecessarily.

## Common Anti-Patterns
Balance as sole truth; direct editable stock count; floating-point quantity; negative stock accidental; untraceable adjustment; transfer writes one side only; no reconciliation; retry duplicating movements.

## Validation Requirements
**Implementation checklist:** stock dimensions; UOM; negative/reservation policy; atomic movement and balances; lock/version; idempotency; valuation boundary; reconciliation. **Review checklist:** concurrent last-unit issue/transfer; rollback; duplicate retry; movement-to-balance reconciliation; authorization scope; audit/adjustment evidence.

## Related Skills
procurement-management, sales-management, production-management, transaction-integrity, concurrency-control, database-integrity.

## Reference Documents
PostgreSQL locking https://www.postgresql.org/docs/current/explicit-locking.html; transactions https://www.postgresql.org/docs/current/tutorial-transactions.html; SAP_CORE_CONTEXT.md. Valuation method and stock dimensions require explicit accounting/product decisions.
