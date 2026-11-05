---
name: production-management
description: Models SAP Core production structures and execution with traceable material consumption and output.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Production Management

## Purpose
Represent manufacturing instructions and execution while reconciling material consumption and finished output.

## Scope
Bills of materials, work centers, production orders, confirmations, component issues, and production receipts.

## When to Use This Skill
Use for BOM, capacity/work center, production order, confirmation, material consumption, or output work.

## When NOT to Use This Skill
Do not implement detailed MRP/capacity planning without a scoped requirement or update inventory balances directly.

## SAP Core Context
Production entities belong to `production`; material/product master data belongs to `master_data`; stock movements belong to `inventory`; financial valuation effects belong to `finance`.

## Core Principles
A BOM is a versioned instruction; a production order snapshots the applicable structure; confirmations are observed execution facts.

## Mandatory Rules
- Version/effectivity changes to BOMs and units must not rewrite released orders' requirements.
- Record planned and actual quantities separately, with UOM conversions and yield/scrap semantics explicit.
- Post component consumption and finished output through inventory's owned movement contract.
- Define order states (planned/released/partially confirmed/complete/closed/cancelled as approved) and legal transitions.
- Make confirmations idempotent and transaction-safe; preserve correction/reversal history.

## Architecture Guidelines
Keep BOM, routing/work-center, and production-order rules in production. A released order captures BOM revision and required components. Consumption and output should reference the order/operation and actor. Coordinate inventory movements as a bounded transaction or documented recoverable use case. Costing/valuation is a finance policy, not an implicit side effect of quantity change.

## Implementation Guidelines
**Example:** Releasing an order for 10 finished units snapshots the active BOM revision and planned material requirement. Confirmation of 8 good units plus 1 scrap records actual component use and 8 output units with linked movements; it does not overwrite planned quantity or infer material from a current BOM revision.

## Data and Security Considerations
Authorize plant/work-center access; audit BOM release, quantity variance, scrap, and confirmation reversal. Preserve lot/serial traceability where the approved product requirements demand it. Avoid exposing labor or cost data to unauthorized roles.

## Common Anti-Patterns
Mutable BOM applied retroactively; planned quantity treated as actual; consumption with no movement; production completion directly sets stock; no scrap/reversal; assumed cost formula; unbounded production report.

## Validation Requirements
**Implementation checklist:** BOM version/effective date; UOM/yield/scrap policy; order snapshot; state transitions; movement contracts; idempotency/concurrency; cost boundary. **Review checklist:** BOM changes do not alter released orders; partial confirmation and correction; inventory reconciliation; unauthorized plant denial; traceable order-to-movement chain.

## Related Skills
inventory-management, master-data-management, transaction-integrity, concurrency-control, database-modeling.

## Reference Documents
SAP_CORE_CONTEXT.md; `inventory-management` and `transaction-integrity`. Manufacturing costing, lot/serial traceability, scheduling, and yield rules need explicit product decisions.
