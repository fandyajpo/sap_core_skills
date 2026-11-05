# Skill Dependency Map

Read each dependent skill's prerequisites before using it. The arrow means **dependent skill → prerequisites**. Every listed edge is a direct dependency; optional relationships are separated below. Global rules and SAP Core context apply to every skill. The direct-dependency graph is intended to be acyclic.

## Direct dependencies

### Architecture & Engineering

- `system-architecture` → none
- `modular-monolith` → `system-architecture`
- `nextjs-engineering` → `react-engineering`, `typescript-engineering`, `system-architecture`
- `react-engineering` → `typescript-engineering`
- `typescript-engineering` → none
- `api-engineering` → `nextjs-engineering`, `typescript-engineering`, `input-validation`, `authentication-engineering`, `authorization-engineering`
- `code-organization` → `modular-monolith`, `typescript-engineering`
- `vite-plus-tooling` → `nextjs-engineering`, `testing-strategy`
- `application-state-management` → `react-engineering`, `tanstack-query`, `zustand-engineering`, `form-engineering`

### Database Engineering

- `postgresql-design` → `database-modeling`
- `prisma-orm` → `postgresql-design`, `database-integrity`, `modular-monolith`
- `prisma-migrate` → `prisma-orm`, `database-integrity`
- `database-modeling` → `modular-monolith`
- `database-integrity` → `postgresql-design`
- `database-performance` → `postgresql-design`, `prisma-orm`
- `database-security` → `postgresql-design`, `prisma-orm`, `application-security`, `authorization-engineering`

### ERP Business Domain

- `erp-domain-modeling` → `modular-monolith`, `database-modeling`
- `core-organization` → `database-modeling`
- `master-data-management` → `core-organization`, `database-modeling`, `input-validation`
- `procurement-management` → `master-data-management`, `inventory-management`, `approval-management`, `transaction-integrity`
- `inventory-management` → `transaction-integrity`, `concurrency-control`, `database-integrity`
- `sales-management` → `master-data-management`, `inventory-management`, `financial-accounting`
- `financial-accounting` → `transaction-integrity`, `database-integrity`, `audit-trail`, `document-numbering`
- `production-management` → `inventory-management`, `master-data-management`, `transaction-integrity`
- `human-resource-management` → `core-organization`, `authorization-engineering`, `database-security`, `audit-trail`

### Enterprise Engineering

- `authentication-engineering` → `nextjs-engineering`, `prisma-orm`, `application-security`
- `authorization-engineering` → `authentication-engineering`, `rbac-authorization`, `application-security`
- `rbac-authorization` → `core-organization`, `audit-trail`
- `transaction-integrity` → `database-integrity`, `concurrency-control`, `erp-domain-modeling`
- `concurrency-control` → `postgresql-design`, `database-integrity`
- `workflow-engine` → `erp-domain-modeling`, `transaction-integrity`
- `approval-management` → `workflow-engine`, `authorization-engineering`, `audit-trail`
- `audit-trail` → `database-integrity`, `application-security`
- `document-numbering` → `transaction-integrity`, `concurrency-control`, `database-integrity`

### Frontend & Experience Engineering

- `tailwind-engineering` → `react-engineering`
- `motion-engineering` → `react-engineering`, `tailwind-engineering`
- `form-engineering` → `input-validation`, `react-engineering`
- `tanstack-query` → `api-engineering`
- `tanstack-table` → `tanstack-query`, `api-engineering`
- `tanstack-virtual` → `tanstack-table`, `performance-optimization`
- `zustand-engineering` → `react-engineering`
- `date-time-engineering` → `input-validation`, `postgresql-design`
- `echarts-visualization` → `tanstack-query`, `performance-optimization`
- `tiptap-editor` → `input-validation`, `application-security`
- `react-pdf` → `performance-optimization`, `application-security`
- `lucide-icons` → `react-engineering`, `tailwind-engineering`
- `sharp-image-processing` → `application-security`, `performance-optimization`
- `xlsx-processing` → `input-validation`, `application-security`, `database-integrity`
- `storybook-engineering` → `react-engineering`, `testing-strategy`

### Quality & Security

- `application-security` → `input-validation`
- `input-validation` → `typescript-engineering`
- `error-handling` → `api-engineering`, `application-security`
- `testing-strategy` → `code-organization`, `application-security`
- `playwright-testing` → `testing-strategy`, `nextjs-engineering`, `authentication-engineering`
- `performance-optimization` → `database-performance`, `observability`
- `caching-strategy` → `tanstack-query`, `nextjs-engineering`, `authorization-engineering`
- `observability` → `error-handling`, `application-security`
- `code-review` → `system-architecture`, `application-security`, `testing-strategy`, `GLOBAL_RULES.md`
- `release-management` → `prisma-migrate`, `testing-strategy`, `observability`, `code-review`

## Optional relationships

- `tanstack-table` may use `tanstack-virtual` only when measured row-rendering cost justifies virtualization.
- `financial-accounting` uses `document-numbering` and `audit-trail` where the document policy requires them; its balance and immutability rules always apply.
- `procurement-management`, `sales-management`, and `production-management` may coordinate with `inventory-management` through explicit application contracts; direct persistence coupling is prohibited.
- `echarts-visualization` may consume aggregated report endpoints; browser code must not query Prisma.
- `storybook-engineering` may use supported interaction testing after verifying the pinned Storybook version.
- `vite-plus-tooling` is optional; do not introduce it into a production Next.js path without verified compatibility.
- The reserved `integration` schema does not authorize external integration implementation; future work requires a new decision.
- `database-performance` informs `performance-optimization`; the latter is broader than database tuning. This is informational and does not form a reverse dependency.

## Cycle and boundary rules

- The graph is keyed by skill slug; verify every slug against `SKILL_INDEX.md` and its directory.
- A dependency is a reading prerequisite, not permission for cross-module persistence access.
- If a proposed edge creates a cycle, extract a stable contract/shared primitive or redesign ownership; do not waive a cycle by renaming packages.
- Authentication produces a validated actor context; authorization consumes it. Session management must not depend on business permissions.
