---
name: sales-management
description: Models SAP Core quotation, order, delivery, and billing lifecycles with traceable fulfillment.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Sales Management

## Purpose
Support controlled customer commitment, fulfillment, and billing while preserving the evidence behind each document.

## Scope
Quotations, sales orders, deliveries, billing documents, customer relationships, pricing snapshots, and stock coordination.

## When to Use This Skill
Use for quote/order lifecycle, fulfillment, delivery, billing, or sales-related customer data.

## When NOT to Use This Skill
Do not turn a sales order into an accounting journal or mutate stock outside inventory's contract.

## SAP Core Context
Sales documents belong to `sales`; customer/products/UOM references belong to `master_data`; stock movements belong to `inventory`; receivables and journal posting belong to `finance`.

## Core Principles
A quotation is an offer, an order is a commitment, delivery is fulfillment evidence, and billing is a financial document. These lifecycles are related, not interchangeable.

## Mandatory Rules
- Preserve accepted price, quantity, currency, tax context, address, and item descriptions needed to reproduce the transaction.
- Define partial order, partial delivery, backorder/cancellation, and billing policies explicitly.
- Reserve/check availability through inventory contracts; do not infer availability from a stale list page.
- Prevent duplicate delivery/billing on retries; authorize each state transition server-side.
- Use exact decimal arithmetic for money and explicit rounding rules.

## Architecture Guidelines
Keep quote/order state and delivery evidence in sales. Coordinate stock reservation/issue with inventory and AR/journal effects with finance. Define whether each step is one local transaction or a recoverable sequence; do not claim cross-module atomicity unless it is actually implemented. Snapshot master data on approval/acceptance where historical terms demand it. Invoice cancellation/correction uses controlled credit/reversal semantics.

## Implementation Guidelines
**Example:** Confirming a sales order validates customer/company/plant and price/tax inputs, checks inventory availability through its contract, and stores accepted terms. A later partial delivery records shipped quantities and inventory issue; billing references delivered/order quantities per policy. A second identical command must not create another delivery or invoice.

## Data and Security Considerations
Scope customer and sales documents by company; protect prices/margins where policy requires. Audit overrides, cancellations, credit, and billing. Do not expose customer records through guessed IDs or reports without authorization.

## Common Anti-Patterns
Order confirmation decrements stock without movement; delivery equals invoice; mutable order price; floating-point totals; client-only stock check; unscoped customer lookup; edit posted invoice instead of correction.

## Validation Requirements
**Implementation checklist:** document state transitions; snapshot fields; pricing/tax/currency rules; stock/finance contracts; partials; authorization; idempotency/audit. **Review checklist:** duplicate confirm/delivery/billing; stock contention; cross-company denial; cancellation/correction trail; totals and rounding.

## Related Skills
master-data-management, inventory-management, financial-accounting, transaction-integrity, document-numbering.

## Reference Documents
SAP_CORE_CONTEXT.md; `inventory-management`, `financial-accounting`, `transaction-integrity`. Tax/pricing policy requires documented product and jurisdiction decisions.
