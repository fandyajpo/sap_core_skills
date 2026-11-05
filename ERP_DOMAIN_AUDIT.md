# ERP Domain Audit

**Date:** 2026-11-05 UTC.  
**Scope:** Domain and lifecycle guidance across the nine ERP Business Domain skills, their relationships to the ten schemas, and the related transaction, authorization, audit, and database skills. This is a design review, not approval of accounting/legal policy or a test of a live ERP implementation.

## Overall result

The domain guidance consistently treats ERP documents as lifecycle-bearing business facts rather than CRUD rows. It preserves the distinction between commitments, observed operational events, and financial postings; assigns each module an owner; requires authorization and company scope; and avoids silently rewriting historical snapshots or posted facts. Cross-module operations are described as explicit contracts/coordinated transactions or recoverable workflows rather than direct table writes.

No domain rule was found that should be silently converted into a universal default. Material decisions are explicitly deferred to product, finance, operations, and qualified legal/accounting owners.

## Domain-by-domain findings

| Domain / owner | Strong guidance observed | Decisions required before implementation |
|---|---|---|
| ERP domain modeling / owning module | Define legal states/transitions, aggregate invariants, snapshots, actor, retries, and correction behavior before CRUD. | Aggregate boundaries, event evidence, compensation/recovery for a workflow that cannot fit one local transaction. |
| Organization / `core` | Distinguish company, organization, and plant; validate company/plant context; separate membership from role/permission; model effective dates and fiscal scope. | Legal entity model, org hierarchy semantics, membership model, fiscal calendar/period boundaries, and whether key relationships receive composite database constraints. |
| Master data / `master_data` | Govern identity, natural keys, duplicate checks, lifecycle, UOM conversions, historical snapshots, and sensitive fields. | Partner identity/role model; business-key normalization/scope; jurisdiction-specific tax identifiers; effective-dated conversions; master-data approval policy. |
| Procurement / `procurement` | Keep requisition, order commitment, receipt observation, and vendor invoice claim distinct; model partials, approval separation, idempotency, and matching evidence. | Approval limits and separation-of-duties policy; over/under-receipt rules; invoice match tolerances; price/terms snapshots; tax and payment policy. |
| Inventory / `inventory` | Treat movements as facts and balances as projections; update atomically; define locking, idempotency, reconciliation, correction movements, and authorization scope. | Canonical stock dimensions; negative-stock and reservation policy; UOM conversion; lot/serial/quality tracking; transfer-in-transit; valuation method and accounting effects. |
| Sales / `sales` | Separate offer, order commitment, fulfillment, and billing; coordinate with inventory/finance owners; preserve accepted terms; make delivery/billing retry-safe. | Price/tax/currency policy; partials, backorders, returns, cancellations, credit notes; stock reservation timing; billing trigger and revenue-recognition policy. |
| Finance / `finance` | Require exact arithmetic, balanced postings, open-period checks, immutable posted rows, source links, idempotency, and reversal/adjustment history. | Accounting standard and chart structure; currencies/scales/rounding; FX policy; tax treatment; ledger/subledger design; fiscal calendars, close/reopen, and posting authorization. |
| Production / `production` | Snapshot released BOM revision; separate planned from actual; record yield/scrap; post consumption/output via inventory contract; preserve correction history. | BOM effectivity, routing/capacity scope, UOM/yield/scrap rules, lot/serial traceability, costing, planning/MRP scope, and variance handling. |
| HR / `human_resources` | Keep departments and positions HR-owned while referencing core org identifiers; distinguish employee from login; limit sensitive fields, access, logs, and exports. | Jurisdictions, legal basis, workforce retention/deletion, attendance rules, payroll scope and formulas, employee identity/relationship, delegated access, and external payroll policy. |
| Cross-cutting / `system` | Number ranges and audit are governed; numbers are distinct from IDs; no gaps/reuse promises without policy; audit is not claimed tamper-proof. | Per-document numbering scope and gap rules; retention/access and legal evidence requirements; workflow rule ownership and versioning. |
| External integration / `integration` | Explicitly reserved; no broker, ERP/accounting/payment/CRM synchronization or provider is assumed. | Any future integration requires a new decision, data-sharing/security model, retry/reconciliation approach, and explicit scope approval. |

## Cross-module integrity review

The strongest examples make responsibility boundaries concrete:

- Procurement owns PO and receipt lifecycle; inventory owns stock movement and balance. Receipt coordination must choose a single bounded PostgreSQL transaction where possible or define durable recovery/compensation if not.
- Sales owns orders and deliveries; inventory owns reservation/issue, and finance owns receivable/journal effects. Availability must be checked through an authoritative inventory contract at command time, not inferred from a cached list.
- Production owns BOM/order/confirmation; inventory owns consumption/output movements and finance owns valuation policy.
- Master data owns reusable partners/products/UOM/chart references; consuming documents preserve the agreed historical values needed to reproduce a transaction.
- Approval is a separate decision bound to the exact reviewed revision; it is neither a posting command nor standing permission.

These are consistent with the modular monolith and do not introduce microservices, external integration, or distributed transaction assumptions.

## Technical precision observations

- Journal debit/credit equality and aggregate stock availability are cross-row invariants. PostgreSQL row-local `CHECK` constraints cannot prove them. The combination of exact arithmetic, transaction, uniqueness/idempotency, locking or conditional update, and reconciliation in the current skills is appropriate.
- Database constraints should protect durable row-local and relational facts, but cross-schema FK use remains subject to governance. A relationship does not confer permission to read or write the referenced module's data.
- Fiscal dates, instants, and named local business time are distinct semantics. The date/time skill cautions that PostgreSQL `timestamptz` stores an instant, not the original IANA timezone.
- Domain examples are explicitly illustrative and state-dependent; they do not claim universal status names or legal treatment.

## Unresolved policy register

1. **Accounting and tax:** applicable standard, ledger scope, tax jurisdictions, currency precision, exchange-rate source/date, rounding, posting periods, and correction policy.
2. **Inventory and manufacturing:** negative stock, reservations, canonical units, batch/serial/quality dimensions, valuation, yield/scrap, costing, and traceability.
3. **Procurement and sales:** approval thresholds, separation-of-duties, match tolerances, accepted terms, returns/credits, backorders, pricing, and billing rules.
4. **Organization and numbering:** legal/operational hierarchy, fiscal calendar, numbering scope/gaps/reuse, period close/reopen, and company/plant integrity enforcement.
5. **HR and privacy:** jurisdictions, purpose/legal basis, retention, access, attendance/payroll obligations, employee lifecycle, and export controls.
6. **Audit and records:** required events, retention duration, evidence access, deletion policy, and tamper-evidence requirements.

Each item should be decided by the named business/legal owner and recorded before the associated schema or workflow hardens. Technical skills should not fill these gaps with generic sample values.

## Recommended sequence

Build the technical foundation and a narrow vertical slice only after runtime versions, schema policy, authentication placement, and a jurisdiction/product scope are chosen. A low-risk sequence is organization and authorization foundations, governed master data, then a single approved document lifecycle with its inventory or finance effect. Validate the selected invariants in PostgreSQL concurrency/integration tests before expanding to other modules. External integrations remain out of scope unless a new decision explicitly changes that boundary.
