---
name: document-numbering
description: Allocates unique, scoped, human-readable SAP Core document identifiers safely under concurrency.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Document Numbering

## Purpose
Create readable business identifiers without conflating them with primary keys or allowing concurrent duplicates.

## Scope
Number ranges, document type, company/fiscal scope, formatting, sequence allocation, uniqueness, and gap policy.

## When to Use This Skill
Use for purchase orders, invoices, journal entries, stock movements, and other user-facing document identifiers.

## When NOT to Use This Skill
Do not use a document number as a primary key, authorization token, or unscoped `MAX()+1` calculation.

## SAP Core Context
`system.number_ranges` and `document_types` are initial platform references. Owning modules define document lifecycle; numbering policy is centrally governed only where consistent.

## Core Principles
A UUID/stable database ID identifies a row; a human number is a business label with scope, lifecycle, and collision policy.

## Mandatory Rules
- Define uniqueness scope: document type, company, fiscal year, plant, or other business dimension as required.
- Protect number uniqueness with a database constraint in addition to allocation logic.
- Allocate atomically under concurrent requests; never calculate next number using `MAX()+1`.
- Document whether gaps are allowed and whether a number is allocated at draft creation or posting; never promise gapless numbering unless a legally validated process supports it.
- Preserve numbers for cancelled/reversed documents according to policy; do not reuse silently.

## Architecture Guidelines
For sequential transactional numbers, a per-scope counter row updated under a transaction/row lock can serialize allocation; ensure correct initialization and unique constraint. PostgreSQL sequences are concurrency-safe but values are not rolled back and can have gaps; choose only when that gap behavior is acceptable and documented. Keep formatting separate from stored sequence value and preserve uniqueness on normalized scope/number. For legal numbering, obtain jurisdictional requirements before implementation.

## Implementation Guidelines
**Example:** A journal number is allocated for `(company, fiscal year, document type)` while creating/posting the entry, with the counter increment and document insert in one controlled transaction. Two concurrent requests serialize on that scope; a unique constraint is the final guard. A retry uses the same idempotency identity rather than allocating a second number.

## Data and Security Considerations
Numbers are predictable and must not be treated as secrets. Authorization uses stable resource IDs and policy checks. Prevent cross-company number collisions according to the chosen scope. Audit range configuration changes and protect number ranges from unauthorized edits.

## Common Anti-Patterns
`MAX()+1`; random display number without uniqueness; one global sequence when legal scope differs; reusing cancelled numbers; promising no gaps with ordinary sequence; number-as-access-control; separate allocation commit followed by an unlinked document insert.

## Validation Requirements
**Implementation checklist:** format/scope; counter mechanism; transaction boundary; idempotency; uniqueness constraint; gap/reuse policy; legal review. **Review checklist:** parallel allocation test; rollback/retry behavior; range exhaustion; year/company boundary; no duplicates; authorization for range administration.

## Related Skills
transaction-integrity, concurrency-control, database-integrity, financial-accounting, workflow-engine.

## Reference Documents
PostgreSQL sequence behavior https://www.postgresql.org/docs/current/functions-sequence.html and explicit locking https://www.postgresql.org/docs/current/explicit-locking.html; SAP_CORE_CONTEXT.md. Legal gapless numbering requirements are jurisdiction-specific and must be verified.
