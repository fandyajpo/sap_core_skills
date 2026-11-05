---
name: procurement-management
description: Models SAP Core purchasing documents and controls from requisition through receipt and invoice matching.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Procurement Management

## Purpose
Implement traceable purchasing workflows that control commitment, approvals, receipt, and vendor invoice evidence.

## Scope
Purchase requisitions, purchase orders, goods receipts, vendor invoices, approval boundaries, and supplier references.

## When to Use This Skill
Use for purchasing lifecycle, supplier-facing records, receipt, invoice matching, or procurement approvals.

## When NOT to Use This Skill
Do not treat procurement as external vendor integration or allow direct stock/accounting writes outside owned contracts.

## SAP Core Context
Purchasing documents belong to `procurement`; vendor/master references belong to `master_data`; physical movements belong to `inventory`; posted accounting belongs to `finance`; organization context belongs to `core`.

## Core Principles
An order is a commitment, a receipt is an observed event, and an invoice is a financial claim. Keep them linked without conflating their quantities or states.

## Mandatory Rules
- Define lifecycle and legal transitions for requisition, PO, receipt, and vendor invoice.
- Enforce approval limits and separation-of-duties on the server.
- Preserve ordered, received, invoiced, and cancelled quantities separately with explicit unit conversion rules.
- Match invoice to order and receipt according to an approved tolerance/policy; discrepancies require a controlled exception, not silent overwrite.
- Record immutable receipt/invoice evidence and prevent duplicate posting/retry effects.

## Architecture Guidelines
A receipt command owned by procurement coordinates with inventory's movement contract; finance posting is through finance's contract. Decide transaction boundary and recoverability. Partial deliveries and invoices must be first-class. Snapshot vendor, price, currency, payment/tax terms as needed at approval/order time. Approval is separate from posting authority. Use exact monetary precision.

## Implementation Guidelines
**Example:** A PO line orders 100 units. A receipt of 60 records a partial receipt and a stock movement of 60 in the canonical stock unit; a second receipt records the remaining quantity. A 100-unit invoice is matched against PO and received quantities; a tolerance exception routes for authorized resolution. Do not set `receivedQuantity = orderedQuantity` because an order was approved.

## Data and Security Considerations
Company/plant/supplier scope checked for every command. Protect vendor tax/bank data. Audit approval, receipt, match override, and cancellation. Do not delete an order referenced by receipts or posted invoices.

## Common Anti-Patterns
One “complete” boolean; PO approval creates stock; invoice automatically equals receipt; received values overwrite ordered quantities; no partials; direct write to inventory/finance tables; floating-point price math.

## Validation Requirements
**Implementation checklist:** document states; quantity/unit/currency/tax rules; approval authority; partial/over-receipt policy; inventory and finance contracts; idempotency/audit. **Review checklist:** duplicate receipt/invoice cases; concurrent approval; mismatch/tolerance; cross-company access; rollback consistency; no posted history mutation.

## Related Skills
master-data-management, inventory-management, approval-management, transaction-integrity, financial-accounting, document-numbering.

## Reference Documents
SAP_CORE_CONTEXT.md; `inventory-management`, `financial-accounting`, `approval-management`. Business tolerances and tax treatment must be product/jurisdiction decisions, not invented defaults.
