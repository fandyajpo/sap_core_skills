# Database Architecture Audit

**Date:** 2026-11-05 UTC.  
**Scope:** Database guidance and schema governance in the SAP Core skills repository. No live database, Prisma schema, migrations, or application source exists here; this is a design/documentation audit, not a schema validation.

## Architecture assessed

The accepted baseline is one database named `sap_core`, PostgreSQL as the engine, Prisma Postgres as the managed platform, Prisma ORM as server-side data access, Prisma Migrate as migration management, and ten logical schemas. The architecture remains a modular monolith. Schema ownership defines who owns business rules and writes; a foreign key does not transfer that ownership.

| Schema | Owner / domain | Initial references in context | Audit note |
|---|---|---|---|
| `core` | Organization and identity-adjacent platform | companies, organizations, plants, fiscal years, user/role/permission references | Better Auth still owns authentication/session mechanics; clarify placement of adapter models in the actual schema. |
| `master_data` | Governed reusable reference data | business partners, customers, vendors, materials, products, UOM, chart of accounts | Business-key scope, nullability, effective dates, and company-specific extensions need explicit model rules. |
| `procurement` | Purchasing | requisitions, purchase orders, receipts, vendor invoices | Preserve ordered/received/invoiced quantities separately and coordinate inventory/finance via owner contracts. |
| `inventory` | Physical stock | locations, balances, movements, transfers | Movement facts and balance projection must remain reconcilable and transaction/concurrency safe. |
| `sales` | Quote/order/fulfillment/billing | quotations, orders, deliveries, billing documents | Stock and AR/journal effects remain owned by inventory and finance. |
| `finance` | Accounting and subledgers | journal entries/lines, AP/AR, cost centers, tax codes | Exact arithmetic, period controls, immutable posted facts, reversals, and idempotency are explicit. |
| `production` | Manufacturing execution | BOMs, work centers, production orders/confirmations | Released orders snapshot approved BOM revisions; quantities and valuation policy need product definition. |
| `human_resources` | Workforce data/processes | employees, departments, positions, attendance, payroll | Sensitive data stays HR-owned; jurisdiction, purpose, access, and retention are unresolved. |
| `integration` | Reserved | None in initial scope | Reservation is not approval to build integrations, callbacks, or a broker. |
| `system` | Cross-cutting platform | audit, settings, number ranges, document types, workflow rules | Use only for genuinely shared platform concerns; retention and administrative ownership need policy. |

## Findings

### Supported design, subject to version pinning

Official Prisma documentation describes PostgreSQL multi-schema models through datasource `schemas` and model/enum `@@schema` mappings, schema-aware migrations and queries, and cross-schema relations. Official release material identifies multi-schema support as generally available in Prisma ORM 6.13. This supports the architecture choice in principle; it does not validate a future SAP Core schema, a specific Prisma major, the selected provider configuration, or the generated client. The Prisma schema and provider versions must be chosen and checked before implementation.

Prisma Postgres documentation currently describes managed PostgreSQL 17 with PgBouncer pooling. It distinguishes the pooled application connection from a direct connection used for migrations. The skill guidance to verify pooling and provider behavior is appropriate; the actual URLs, secrets, TLS, network controls, backup plan, and runtime are not configured in this repository.

### Constraint and type semantics

- PostgreSQL 17 `CHECK` constraints are row-local and pass when the expression is `TRUE` **or `NULL`**. Required values need `NOT NULL` in addition to a value check.
- PostgreSQL does not guarantee cross-row invariants through `CHECK`. A journal's debit/credit total and available stock across records need an explicit transactional/concurrency design and reconciliation; do not model them as a subquery in a `CHECK`.
- PostgreSQL unique constraints treat nulls as distinct by default. A nullable natural-key component can therefore permit more than one row with null in that component. Set required key parts `NOT NULL` or explicitly design a version-supported null/partial uniqueness policy.
- A foreign key ensures the referenced key exists; optional `NULL` references can bypass matching unless nullability is constrained. `ON DELETE` and `ON UPDATE` behavior must be chosen for business lifecycle, not accepted as an accidental default.
- PostgreSQL does not automatically index the referencing FK columns. Index them when lookup, delete/update checks, or measured workload justifies the additional write/storage cost.
- PostgreSQL `numeric`/`decimal` is exact, but declared precision/scale can round inputs at storage. Define scale, currency/unit, range, and rounding policy explicitly and preserve decimal values as exact representations across Prisma and JSON boundaries.
- PostgreSQL `date` is calendar-date semantics. `timestamptz` represents an instant normalized internally to UTC and does not preserve the original named timezone; keep the business timezone separately when future local-wall-time meaning is required.

The correction to `Database Engineering/postgresql-design/SKILL.md` records the first four constraint/index semantics above. Existing `database-integrity`, `financial-accounting`, `inventory-management`, and `date-time-engineering` guidance already covers the complementary transaction, exact arithmetic, reconciliation, and timezone rules.

### Invariants and enforcement placement

| Invariant | Appropriate durable control | Audit assessment |
|---|---|---|
| Required non-null business identifiers and bounded row values | `NOT NULL`, row-local `CHECK`, type/range constraints | Guidance was clarified for `CHECK`/`NULL`. Confirm each column's business nullability when modeled. |
| Unique scoped document/natural key | Unique constraint/index with company/type/year scope; idempotency identity for retries | Skills correctly reject application-only duplicate checks and `MAX()+1`. Decide null behavior, normalization, and whether inactive records participate. |
| Reference belongs to correct company/plant | Composite unique/reference keys where suitable and supported; otherwise owner-contract validation in a transaction | Cross-schema FKs are supported in documented Prisma/PostgreSQL arrangements, but their use is explicitly subject to governance approval. Decide common pairs such as `(company_id, plant_id)` before schema design. |
| Balanced posted journal | Exact decimal computation, bounded transaction for header/lines/state, uniqueness/idempotency, immutable posting, reconciliation | No row-level `CHECK` can total lines across records. Existing skills state the correct cross-row transaction boundary. |
| Stock movement and balance agree | Movement plus balance update in one transaction; locking/conditional update/version protocol; idempotency and reconciliation | Existing guidance is strong. Define canonical dimensions, negative-stock/reservation and valuation policies before encoding constraints. |
| Document state transition and approval refer to reviewed revision | State/version conditional write, unique decision identity, transaction and audit | Existing workflow/approval/concurrency guidance covers changed-state races and reapproval. |
| Audit evidence accompanies committed action | Same transaction or a reliable, explicitly designed alternative; append-oriented and access-restricted | Existing guidance correctly avoids calling application audit rows tamper-proof. |

## Migration and operational audit

The migration guidance correctly requires review of generated SQL, old/new application compatibility, restartable bounded backfills, immutable applied history, safe destructive-change handling, and a forward recovery plan. It correctly avoids prescribing one command across Prisma versions.

This audit verified official documentation showing that the Prisma migration workflow differs by major. Prisma ORM 7 documentation uses the established deploy workflow; the current Prisma ORM 8 release-candidate docs describe contract planning and `db migrate`. Do not copy either workflow until the actual Prisma CLI version is pinned. For Prisma Postgres, verify direct versus pooled connection use for migrations. No migration was generated, inspected, or applied in this audit.

Recommended implementation checks once the application exists: fresh database creation; upgrade from the previous schema; all ten schemas represented; cross-schema foreign-key create/drop and generated client behavior; migration through the direct provider connection; query runtime through the pooled connection; constraint edge cases; concurrency and rollback; and restore/reconciliation procedures.

## Unresolved decisions

1. Data-governance policy for cross-schema foreign keys versus module contract enforcement, including company/plant and shared master references.
2. Placement/ownership of Better Auth adapter models and migration ownership within the ten-schema model.
3. Selected Prisma ORM/CLI version and its supported PostgreSQL/provider features; exact `numeric` mapping; raw SQL and unsupported Prisma-feature governance.
4. PostgreSQL 17 service major confirmation, pool/direct connection configuration, migration identity, runtime privileges, backup/restore objectives, and deployment secrets mechanism.
5. Domain decisions: financial scales/currencies, fiscal calendar, stock dimensions/valuation, tax/account rules, document-number scope/gaps, and retention.

## Official references checked

- PostgreSQL 17 constraints: https://www.postgresql.org/docs/17/ddl-constraints.html
- PostgreSQL numeric and date/time: https://www.postgresql.org/docs/17/datatype-numeric.html and https://www.postgresql.org/docs/17/datatype-datetime.html
- PostgreSQL isolation, locks, sequences: https://www.postgresql.org/docs/17/transaction-iso.html, https://www.postgresql.org/docs/17/explicit-locking.html, and https://www.postgresql.org/docs/17/functions-sequence.html
- Prisma multi-schema: https://www.prisma.io/docs/orm/v7/prisma-schema/data-model/multi-schema
- Prisma migration workflow: https://www.prisma.io/docs/orm/migrations/applying-a-migration
- Prisma transactions: https://www.prisma.io/docs/orm/fundamentals/transactions
- Prisma Postgres: https://www.prisma.io/docs/postgres

See [TECHNICAL_COMPATIBILITY_MATRIX.md](TECHNICAL_COMPATIBILITY_MATRIX.md) for version-specific applicability and [CORRECTION_LOG.md](CORRECTION_LOG.md) for the exact text change.
