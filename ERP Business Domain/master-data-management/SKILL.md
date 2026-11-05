---
name: master-data-management
description: Defines governed, reusable SAP Core master data and its lifecycle across ERP modules.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Master Data Management

## Purpose
Provide authoritative shared records with controlled creation, change, deduplication, and retirement.

## Scope
Business partners, customers, vendors, materials, products, units of measure, chart of accounts references, and data ownership.

## When to Use This Skill
Use when creating, importing, validating, or changing records reused by multiple modules.

## When NOT to Use This Skill
Do not store transaction-specific values as mutable master data or build external synchronization before approved.

## SAP Core Context
Master records belong to `master_data`; company/plant context is in `core`; finance owns posted accounting facts. Use explicit module contracts and company scope.

## Core Principles
One authoritative identity per governed entity; lifecycle and duplicate policy are explicit; transactional documents preserve relevant snapshots.

## Mandatory Rules
- Define which domain owns each master entity and allowed consumers.
- Use business keys with explicit scope and normalization; protect uniqueness at the database boundary where applicable.
- Model customer/vendor as business-partner roles over a governed identity only if the domain supports shared identity, not by assuming every partner has both roles.
- Define units and conversion factors with dimensional meaning and effective dates; never compare raw quantities from unlike units.
- Inactivation must preserve historical references and must not silently invalidate posted transactions.

## Architecture Guidelines
Use Zod to validate inbound master-data commands and duplicate checks; database uniqueness protects concurrent creation. Establish approval or dual-control requirements for sensitive account, tax, or payment attributes. Customer/vendor/material data may have company-specific extensions but retain a clear owner and relationship. Chart of accounts in master-data is reference structure; finance owns journal behavior and posting. No external master-data sync is in initial scope.

## Implementation Guidelines
**Example:** Before creating a vendor, normalize the approved business key (such as company-scoped registration/tax identifier if policy permits), search possible duplicates, then persist with a database unique constraint to close the race. When a sales order is accepted, copy the required customer name/address/price/tax snapshot onto the document so later master edits do not rewrite history.

## Data and Security Considerations
Classify personal, tax, bank, and contact data; restrict fields and audit sensitive edits. Apply company scope consistently. Do not expose duplicate-match candidates to unauthorized users. Avoid collecting sensitive attributes without a stated purpose and retention rule.

## Common Anti-Patterns
Free-text partner names on each transaction; duplicate prevention only in browser; destructive delete; mutable snapshots; unversioned UOM conversions; using display names as foreign keys; treating import integration as in scope.

## Validation Requirements
**Implementation checklist:** owner; natural-key scope/normalization; duplicate policy; lifecycle; effective dates; consumer contract; snapshot semantics; sensitive-field policy. **Review checklist:** concurrent duplicate test; invalid UOM/tax/account reference rejected; historical document stable; cross-company visibility tested.

## Related Skills
core-organization, database-modeling, input-validation, procurement-management, sales-management.

## Reference Documents
SAP_CORE_CONTEXT.md; Zod https://zod.dev/; PostgreSQL unique constraints https://www.postgresql.org/docs/current/ddl-constraints.html. Jurisdiction-specific tax/account rules require explicit product/legal validation.
