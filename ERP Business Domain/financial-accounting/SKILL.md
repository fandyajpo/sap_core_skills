---
name: financial-accounting
description: Enforces double-entry, immutable posting, period control, and auditable financial history in SAP Core.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Financial Accounting

## Purpose
Preserve reliable, balanced, auditable accounting facts and controlled subledger workflows.

## Scope
Chart of accounts, journal entries/lines, AP/AR, cost centers, tax codes, fiscal periods, posting, reversal, and adjustment.

## When to Use This Skill
Use for financial documents, posting, period close, account mapping, amounts, or financial reports.

## When NOT to Use This Skill
Do not change a posted entry in place, use floating point for money, or treat a subledger document as automatically posted without an explicit rule.

## SAP Core Context
Journal and subledger entities belong to `finance`; chart of accounts is referenced from `master_data`; fiscal years/organization are in `core`; operational source documents remain owned by their modules.

## Core Principles
A posted journal entry is an immutable business fact. Corrections preserve history through reversal or adjustment; double-entry totals balance per the defined ledger/currency policy.

## Mandatory Rules
- Validate debits equal credits at the posting boundary using exact decimal arithmetic and explicitly defined currency/rounding rules.
- Reject posting to closed/nonexistent fiscal periods and unauthorized accounts/company scope.
- Separate draft/editable data from posted immutable entries and lines.
- Reversal/adjustment references the original and records actor, time, reason, and resulting entries.
- Preserve source-document linkage and posting idempotency; prevent duplicate financial effects.

## Architecture Guidelines
Use PostgreSQL exact `numeric` representations through a verified Prisma mapping; do not use JavaScript binary floating point for authoritative totals. Model currency and exchange-rate source/date/precision explicitly if multi-currency is in scope. A transaction creates journal header and all lines, verifies balance, and changes posting state atomically. Do not invent jurisdiction-specific accounting/tax treatment: encode approved policy. Reports use authorized company/period/account filters and reconcile to posted ledger facts.

## Implementation Guidelines
**Example:** `post` accepts an approved draft/version, checks period is open, validates account/company/cost-center permissions and line totals, then commits immutable header/lines plus a unique posting reference in one transaction. A correction creates a reversing entry with reciprocal lines and a reason; it does not update the posted rows.

## Data and Security Considerations
Restrict sensitive balances and payroll/finance reports. Audit edits to drafts, approvals, postings, reversals, and period changes. Prevent exposure through aggregate endpoints and exports. Preserve decimal precision across serialization; never round implicitly in UI.

## Common Anti-Patterns
`float` amounts; one-sided entries; posted-row edits/deletes; silent rounding; posting outside period; duplicate retry; mutable account mapping changing historical meaning; finance posting buried in sales/procurement UI.

## Validation Requirements
**Implementation checklist:** currency/scale/rounding; balanced totals; open period; immutable state; source/idempotency key; transaction; reversal path; audit. **Review checklist:** exact-decimal tests; unequal totals rejected; concurrent duplicate posting; closed-period denial; reversal trace; access and export scope; reconciliation.

## Related Skills
transaction-integrity, database-integrity, audit-trail, document-numbering, sales-management, procurement-management.

## Reference Documents
PostgreSQL numeric types https://www.postgresql.org/docs/current/datatype-numeric.html; PostgreSQL transactions https://www.postgresql.org/docs/current/tutorial-transactions.html; SAP_CORE_CONTEXT.md. Accounting policy, currency conversion, and tax rules require explicit domain/jurisdiction approval.
