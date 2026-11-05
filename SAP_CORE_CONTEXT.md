# SAP Core Context

This document is the single source of truth for the product and engineering baseline. Skills may add domain-specific detail but must not contradict it. Changes to this baseline require a recorded decision.

## Product definition

SAP Core is an ERP platform being built from an empty foundation. The first priority is a secure application foundation, authentication and authorization, governed PostgreSQL data and migrations, core organization and master data, module foundations, an internal REST API, frontend foundations, and transaction-safe business workflows. ERP records represent business facts and lifecycle transitions, not merely CRUD rows.

## Architecture

- Begin as one deployable **modular monolith** with domain-oriented module boundaries.
- Each module owns its domain rules and writes to its owned tables through its application/domain boundary. Cross-module access uses explicit contracts or application services; direct unrestricted queries against another module's tables are prohibited.
- Shared infrastructure (database client, authentication plumbing, logging, common primitives) is centrally governed and must not become a business-logic dumping ground.
- Use an internal REST API with validated contracts, authentication, authorization, pagination, and stable error semantics.
- Keep business operations transaction-safe. Do not add microservices, distributed transactions, an external message broker, or external integration infrastructure without a separately approved requirement.

## Approved technology stack and responsibilities

| Concern | Approved technology | Responsibility / boundary |
|---|---|---|
| Web application | Next.js, React, TypeScript | Next.js owns application routing/rendering and server boundaries; React owns UI composition; TypeScript provides static contracts, not runtime validation. |
| Styling and interaction | Tailwind CSS, Motion (motion.dev) | Tailwind is the primary styling system; Motion is for purposeful, accessible interaction animation. |
| Development toolchain | Vite+ | Evaluate as a development toolchain only. Do not assume it replaces Next.js routing, compilation, bundling, or production build. Verify concrete compatibility before use. |
| Database platform / engine | Prisma Postgres / PostgreSQL | Prisma Postgres is the managed platform; PostgreSQL is the relational database engine. |
| Data access / migrations | Prisma ORM / Prisma Migrate | Prisma ORM is the server-side data-access layer; Prisma Schema is the primary model declaration; Prisma Migrate governs schema evolution. |
| Authentication / authorization | Better Auth / CASL | Better Auth identifies users and manages sessions; CASL models permissions. Every protected action is enforced server-side. |
| Validation / forms | Zod / React Hook Form | Zod performs runtime validation; React Hook Form manages interactive form state. Client validation never replaces server validation. |
| Server and client state | TanStack Query, Zustand, React state, URL state | TanStack Query owns remote/server state; Zustand owns shared client/UI state; local React state is component-local; URL parameters own shareable navigation/filter state. |
| Data-heavy UI | TanStack Table, TanStack Virtual | Table state/columns and virtualization; ERP-scale filtering, ordering, and pagination should be server-side. |
| Dates | date-fns | Date parsing/formatting utilities; persist instants and business dates according to explicit semantics, not browser locale assumptions. |
| Visualization / rich content | Apache ECharts, Tiptap, React PDF, Lucide React | Charts, rich-text editing, PDF rendering, and icons respectively; each has bounded responsibilities and security/performance controls. |
| File/data processing | Sharp, XLSX | Server-side image processing and spreadsheet parsing/generation with resource limits and untrusted-file handling. |
| Quality | Playwright, Storybook | Browser-level tests and isolated component documentation/testing. |
| API | REST | Internal resource-oriented application interface; no external integration platform in the initial scope. |

Do not replace an approved technology with an alternative without documenting a concrete limitation and an approved architectural decision. Do not add every library to every module.

## Database architecture

- Database name: `sap_core`.
- Managed PostgreSQL platform: Prisma Postgres.
- Engine: PostgreSQL.
- ORM: Prisma ORM.
- Migration system: Prisma Migrate.
- One database, ten logical schemas: `core`, `master_data`, `procurement`, `inventory`, `sales`, `finance`, `production`, `human_resources`, `integration`, and `system`.
- Use relational modeling, foreign keys, unique/check constraints, suitable indexes, exact `numeric` precision for money/quantities where business rules require it, explicit timestamp/time-zone semantics, and transaction boundaries.
- UUIDs are the baseline identifier choice unless a documented domain requirement selects otherwise. Document identifiers shown to users (document numbers) are not primary keys.
- Avoid storing relational facts in JSON. Use JSON/JSONB only for genuinely variable, non-relational payloads with bounded, documented semantics.
- Prisma's support and syntax for multi-schema mapping, provider features, generated client behavior, and migration operations depend on the selected Prisma version and datasource. Verify against the pinned version before implementing; do not infer behavior from this overview.

### Schema ownership and initial entities

| Schema | Owning domain | Initial entity references (not exhaustive) |
|---|---|---|
| `core` | Identity-adjacent platform and organization | users, roles, permissions, companies, plants, organizations, fiscal_years |
| `master_data` | Reusable business reference/master records | business_partners, customers, vendors, materials, products, units_of_measure, chart_of_accounts |
| `procurement` | Buying lifecycle | purchase_requisitions, purchase_orders, goods_receipts, vendor_invoices |
| `inventory` | Physical stock and movement | storage_locations, stock_balances, stock_movements, stock_transfers |
| `sales` | Quote-to-billing operational lifecycle | quotations, sales_orders, deliveries, billing_documents |
| `finance` | Accounting and financial subledgers | journal_entries, journal_lines, accounts_payable, accounts_receivable, cost_centers, tax_codes |
| `production` | Manufacturing execution | bills_of_materials, work_centers, production_orders, production_confirmations |
| `human_resources` | Workforce records and processes | employees, departments, positions, attendance, payroll |
| `integration` | Reserved for possible future integration capabilities | No initial integration engine or third-party synchronization is in scope. |
| `system` | Cross-cutting platform configuration and records | audit_logs, system_settings, number_ranges, document_types, workflow_rules |

These are starting references, not a mandate to create unused tables. Every new entity needs an owner, business justification, lifecycle, constraints, audit policy, and migration plan. Cross-schema foreign keys may be used only where supported and explicitly approved by the data-governance policy; ownership does not transfer with a foreign key.

## ERP invariants

- A business document has explicit states and legal transitions; posted/confirmed history is not edited as if it were a draft.
- Posted journal entries are immutable. Correct with reversals or adjustment entries. Each posting balances debits and credits using exact decimal arithmetic.
- Inventory movements are traceable facts. Stock balances are transactionally maintained projections and must be reconcilable to movement history. Concurrent updates require a deliberate locking or optimistic-concurrency strategy.
- Organization/company boundaries are part of authorization and query scoping, not merely UI filters.
- Number allocation must be safe under concurrency and must not rely on `MAX(number) + 1`.
- Audit history must identify actor, time, action, target, and relevant change context without leaking credentials or unnecessary sensitive data.

## Development priorities

1. Application foundation.
2. Authentication and authorization.
3. PostgreSQL/database governance, Prisma ORM and migrations.
4. Core organization and master data.
5. ERP module foundations.
6. Internal REST API.
7. Frontend foundation.
8. Business workflows and transaction processing.

## Explicitly deferred

No third-party database, external ERP/accounting/payment/CRM platform, synchronization service, external message broker, or enterprise service provider is to be integrated in the initial foundation. The `integration` schema remains reserved. Future integration must be a deliberate decision and must not compromise current module ownership or transaction integrity.

## Version and reference policy

The repository does not prescribe package versions. Before implementing version-dependent APIs, adapters, configuration flags, or framework behavior, inspect the application's pinned versions and consult the corresponding official documentation/release notes. Reference links in individual skills identify authoritative starting points; they do not constitute a claim that a particular unpinned version was tested. Record the verification date and version when a claim is checked.
