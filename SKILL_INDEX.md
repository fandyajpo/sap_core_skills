# Skill Index

This index covers all 59 skills across six categories. Each skill's directory contains the authoritative `SKILL.md`; read its direct prerequisites in [SKILL_DEPENDENCY_MAP.md](SKILL_DEPENDENCY_MAP.md) and its references before implementation.

## Architecture & Engineering (9)

| Skill | Purpose | When to use | Related skills |
|---|---|---|---|
| [system-architecture](Architecture%20%26%20Engineering/system-architecture/SKILL.md) | Product-wide boundaries and architectural decisions. | Cross-cutting design or change to platform structure. | modular-monolith, code-organization, database-modeling, api-engineering |
| [modular-monolith](Architecture%20%26%20Engineering/modular-monolith/SKILL.md) | Module ownership, contracts, and dependency direction. | Creating modules or cross-domain workflows. | system-architecture, code-organization, erp-domain-modeling |
| [nextjs-engineering](Architecture%20%26%20Engineering/nextjs-engineering/SKILL.md) | Next.js app/server boundaries for SAP Core. | Routes, rendering, server actions/handlers, framework configuration. | react-engineering, typescript-engineering, api-engineering, authentication-engineering |
| [react-engineering](Architecture%20%26%20Engineering/react-engineering/SKILL.md) | Component composition and UI behavior. | Building reusable React screens and components. | nextjs-engineering, typescript-engineering, form-engineering, application-state-management |
| [typescript-engineering](Architecture%20%26%20Engineering/typescript-engineering/SKILL.md) | Static typing and safe boundary modeling. | Any TypeScript implementation or contract change. | input-validation, code-organization, api-engineering |
| [api-engineering](Architecture%20%26%20Engineering/api-engineering/SKILL.md) | Internal REST contracts and server boundary rules. | Adding or changing an endpoint/resource. | nextjs-engineering, input-validation, authentication-engineering, authorization-engineering |
| [code-organization](Architecture%20%26%20Engineering/code-organization/SKILL.md) | Repository layout, dependency direction, and naming. | Adding files/modules or refactoring boundaries. | modular-monolith, typescript-engineering, system-architecture |
| [vite-plus-tooling](Architecture%20%26%20Engineering/vite-plus-tooling/SKILL.md) | Safe, evidence-based use of Vite+ alongside Next.js. | Evaluating tooling tasks or build compatibility. | nextjs-engineering, testing-strategy, system-architecture |
| [application-state-management](Architecture%20%26%20Engineering/application-state-management/SKILL.md) | Explicit ownership of remote, UI, local, form, and URL state. | Choosing where frontend state belongs. | tanstack-query, zustand-engineering, form-engineering, react-engineering |

## Database Engineering (7)

| Skill | Purpose | When to use | Related skills |
|---|---|---|---|
| [postgresql-design](Database%20Engineering/postgresql-design/SKILL.md) | PostgreSQL relational design, types, constraints, and indexes. | Designing or tuning persistent structures. | database-modeling, database-integrity, database-performance, database-security |
| [prisma-orm](Database%20Engineering/prisma-orm/SKILL.md) | Server-only Prisma data access and query patterns. | Implementing data access or changing Prisma models. | postgresql-design, prisma-migrate, database-integrity, modular-monolith |
| [prisma-migrate](Database%20Engineering/prisma-migrate/SKILL.md) | Safe versioned Prisma Migrate evolution. | Any schema change, backfill, or release rollout. | prisma-orm, database-integrity, release-management |
| [database-modeling](Database%20Engineering/database-modeling/SKILL.md) | Entity ownership, relationships, lifecycle, and schema governance. | Creating/changing ERP entities or boundaries. | erp-domain-modeling, postgresql-design, modular-monolith |
| [database-integrity](Database%20Engineering/database-integrity/SKILL.md) | Constraints, atomic writes, and reconcilable invariants. | Any operation spanning related rows or business facts. | postgresql-design, prisma-orm, transaction-integrity, concurrency-control |
| [database-performance](Database%20Engineering/database-performance/SKILL.md) | Evidence-driven query/index and workload optimization. | Slow queries, large tables, or capacity-sensitive paths. | postgresql-design, prisma-orm, tanstack-table, performance-optimization |
| [database-security](Database%20Engineering/database-security/SKILL.md) | Credential, access, data exposure, and query safety controls. | Database configuration, access path, or sensitive data change. | postgresql-design, prisma-orm, application-security, authorization-engineering |

## ERP Business Domain (9)

| Skill | Purpose | When to use | Related skills |
|---|---|---|---|
| [erp-domain-modeling](ERP%20Business%20Domain/erp-domain-modeling/SKILL.md) | Model business documents, lifecycles, and invariants. | New ERP capability or workflow boundary. | modular-monolith, database-modeling, transaction-integrity |
| [core-organization](ERP%20Business%20Domain/core-organization/SKILL.md) | Companies, organizations, plants, fiscal years, and user context. | Organization structure or scope changes. | master-data-management, authorization-engineering, database-modeling |
| [master-data-management](ERP%20Business%20Domain/master-data-management/SKILL.md) | Govern reusable business partner/product/reference records. | Creating or changing shared master data. | core-organization, database-modeling, input-validation |
| [procurement-management](ERP%20Business%20Domain/procurement-management/SKILL.md) | Requisition-to-receipt/invoice controls. | Procurement lifecycle and approval work. | master-data-management, inventory-management, approval-management, transaction-integrity |
| [inventory-management](ERP%20Business%20Domain/inventory-management/SKILL.md) | Stock ledger, balances, transfers, and reconciliation. | Any stock-affecting operation. | procurement-management, sales-management, transaction-integrity, concurrency-control |
| [sales-management](ERP%20Business%20Domain/sales-management/SKILL.md) | Quote-to-order, delivery, and billing lifecycle. | Sales documents and fulfillment. | master-data-management, inventory-management, financial-accounting |
| [financial-accounting](ERP%20Business%20Domain/financial-accounting/SKILL.md) | Double-entry posting, periods, subledgers, and corrections. | Accounting or financial posting/reporting. | transaction-integrity, database-integrity, audit-trail, document-numbering |
| [production-management](ERP%20Business%20Domain/production-management/SKILL.md) | BOM, work-center, order, consumption, and output controls. | Manufacturing planning/execution. | inventory-management, master-data-management, transaction-integrity |
| [human-resource-management](ERP%20Business%20Domain/human-resource-management/SKILL.md) | Employee lifecycle and restricted HR records. | Workforce, attendance, and payroll foundations. | core-organization, authorization-engineering, database-security, audit-trail |

## Enterprise Engineering (9)

| Skill | Purpose | When to use | Related skills |
|---|---|---|---|
| [authentication-engineering](Enterprise%20Engineering/authentication-engineering/SKILL.md) | Better Auth identity and session lifecycle. | Login, session, password, account, or identity changes. | nextjs-engineering, prisma-orm, application-security |
| [authorization-engineering](Enterprise%20Engineering/authorization-engineering/SKILL.md) | CASL policy and server enforcement. | Protected action/resource or organization-boundary change. | authentication-engineering, rbac-authorization, api-engineering |
| [rbac-authorization](Enterprise%20Engineering/rbac-authorization/SKILL.md) | Role assignment, permission composition, and scope. | Role/permission model changes. | authorization-engineering, core-organization, audit-trail |
| [transaction-integrity](Enterprise%20Engineering/transaction-integrity/SKILL.md) | Atomic business operations and consistency boundaries. | Multi-record posting, stock, or lifecycle transition. | database-integrity, concurrency-control, erp-domain-modeling |
| [concurrency-control](Enterprise%20Engineering/concurrency-control/SKILL.md) | Safe concurrent updates, locks, versions, and retries. | Shared stock, numbering, approvals, or competing writes. | transaction-integrity, postgresql-design, database-integrity |
| [workflow-engine](Enterprise%20Engineering/workflow-engine/SKILL.md) | Explicit state transitions and workflow execution. | Multi-step document lifecycles. | approval-management, erp-domain-modeling, transaction-integrity |
| [approval-management](Enterprise%20Engineering/approval-management/SKILL.md) | Attributable, scoped approval decisions. | Approval routing, decisions, and re-approval rules. | workflow-engine, authorization-engineering, audit-trail |
| [audit-trail](Enterprise%20Engineering/audit-trail/SKILL.md) | Traceable, privacy-conscious business evidence. | Sensitive changes, postings, approvals, or compliance reports. | database-integrity, application-security, financial-accounting |
| [document-numbering](Enterprise%20Engineering/document-numbering/SKILL.md) | Concurrent-safe human-readable identifiers. | Allocating document numbers in any business module. | transaction-integrity, concurrency-control, database-integrity |

## Frontend & Experience Engineering (15)

| Skill | Purpose | When to use | Related skills |
|---|---|---|---|
| [tailwind-engineering](Frontend%20%26%20Experience%20Engineering/tailwind-engineering/SKILL.md) | Consistent utility styling and responsive UI. | Styling SAP Core screens/components. | react-engineering, storybook-engineering |
| [motion-engineering](Frontend%20%26%20Experience%20Engineering/motion-engineering/SKILL.md) | Accessible purposeful animation with Motion. | Meaningful interaction transitions. | react-engineering, tailwind-engineering |
| [form-engineering](Frontend%20%26%20Experience%20Engineering/form-engineering/SKILL.md) | React Hook Form and Zod form patterns. | User input, validation, and submission. | input-validation, react-engineering, api-engineering |
| [tanstack-query](Frontend%20%26%20Experience%20Engineering/tanstack-query/SKILL.md) | Remote state, cache, mutation, and invalidation. | Fetching/mutating internal REST data. | application-state-management, api-engineering |
| [tanstack-table](Frontend%20%26%20Experience%20Engineering/tanstack-table/SKILL.md) | ERP data-grid semantics and server operations. | List/report grids with sorting/filtering/paging. | tanstack-query, tanstack-virtual, api-engineering |
| [tanstack-virtual](Frontend%20%26%20Experience%20Engineering/tanstack-virtual/SKILL.md) | Bounded rendering for large result sets. | Long lists/grids where DOM cost is measured. | tanstack-table, performance-optimization |
| [zustand-engineering](Frontend%20%26%20Experience%20Engineering/zustand-engineering/SKILL.md) | Shared client-only application/UI state. | Cross-component transient state. | application-state-management, react-engineering |
| [date-time-engineering](Frontend%20%26%20Experience%20Engineering/date-time-engineering/SKILL.md) | Correct business date, instant, timezone, and display semantics. | Date/time inputs, filters, storage, or reports. | postgresql-design, input-validation |
| [echarts-visualization](Frontend%20%26%20Experience%20Engineering/echarts-visualization/SKILL.md) | Accurate and accessible ERP charts. | Dashboards and analytical visualizations. | tanstack-query, performance-optimization |
| [tiptap-editor](Frontend%20%26%20Experience%20Engineering/tiptap-editor/SKILL.md) | Controlled rich-text editing and safe persistence. | Notes/descriptions requiring rich text. | input-validation, application-security |
| [react-pdf](Frontend%20%26%20Experience%20Engineering/react-pdf/SKILL.md) | Bounded PDF rendering/generation workflows. | Document preview or PDF output. | performance-optimization, audit-trail |
| [lucide-icons](Frontend%20%26%20Experience%20Engineering/lucide-icons/SKILL.md) | Consistent accessible icon usage. | Adding interface icons. | react-engineering, tailwind-engineering |
| [sharp-image-processing](Frontend%20%26%20Experience%20Engineering/sharp-image-processing/SKILL.md) | Safe server-side image validation and transformation. | Image upload, thumbnail, or derived asset processing. | application-security, performance-optimization |
| [xlsx-processing](Frontend%20%26%20Experience%20Engineering/xlsx-processing/SKILL.md) | Safe spreadsheet import/export. | ERP data import/export workflows. | input-validation, application-security, database-integrity |
| [storybook-engineering](Frontend%20%26%20Experience%20Engineering/storybook-engineering/SKILL.md) | Isolated UI documentation and component states. | Shared component development and review. | react-engineering, tailwind-engineering, testing-strategy |

## Quality & Security (10)

| Skill | Purpose | When to use | Related skills |
|---|---|---|---|
| [application-security](Quality%20%26%20Security/application-security/SKILL.md) | Threat-aware application controls and secure defaults. | Every feature and security-sensitive change. | authentication-engineering, authorization-engineering, database-security |
| [input-validation](Quality%20%26%20Security/input-validation/SKILL.md) | Runtime validation at HTTP, form, and file boundaries. | Any untrusted input or contract change. | typescript-engineering, api-engineering, form-engineering |
| [error-handling](Quality%20%26%20Security/error-handling/SKILL.md) | Stable safe errors and actionable internal diagnostics. | API/use-case failure paths. | api-engineering, observability, application-security |
| [testing-strategy](Quality%20%26%20Security/testing-strategy/SKILL.md) | Risk-based unit, integration, and browser test layers. | New behavior, regression, or architecture change. | playwright-testing, storybook-engineering, release-management |
| [playwright-testing](Quality%20%26%20Security/playwright-testing/SKILL.md) | Reliable browser-level user journey tests. | Authentication and ERP end-to-end flows. | testing-strategy, nextjs-engineering |
| [performance-optimization](Quality%20%26%20Security/performance-optimization/SKILL.md) | Measure and improve user/database/resource performance. | Slow routes, queries, rendering, files, or reports. | database-performance, tanstack-virtual, caching-strategy |
| [caching-strategy](Quality%20%26%20Security/caching-strategy/SKILL.md) | Correct cache ownership, freshness, and invalidation. | Server/client caching or mutation behavior. | tanstack-query, nextjs-engineering, authorization-engineering |
| [observability](Quality%20%26%20Security/observability/SKILL.md) | Correlated, privacy-safe operational diagnostics. | Logging, metrics, traces, and supportability. | error-handling, audit-trail, application-security |
| [code-review](Quality%20%26%20Security/code-review/SKILL.md) | Enforceable architecture, security, and data review. | Reviewing every implementation or migration. | GLOBAL_RULES.md, all affected skills |
| [release-management](Quality%20%26%20Security/release-management/SKILL.md) | Safe version, migration, rollout, and rollback practice. | Releasing application or database changes. | prisma-migrate, testing-strategy, observability |
