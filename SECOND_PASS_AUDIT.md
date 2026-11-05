# Second-Pass Technical Audit

**Status:** Complete — documentation-only review.  
**Audit date:** 2026-11-05 (UTC).  
**Scope:** All 59 skill entry points, the supplemental TanStack Query reference, and the root architecture, database, security, ERP, index, dependency, and generation documents.

## Executive result

The repository is a coherent engineering knowledge base, not an application implementation. No confirmed critical data-integrity, authorization, or architecture defect was found in the guidance reviewed. The most important release gate is unresolved framework/provider version selection: the repository has no SAP Core application lockfile, and current official Prisma documentation describes major-version differences that affect both migrations and transactions. A concrete PostgreSQL constraint-semantics omission was corrected in the PostgreSQL skill. Other open points require product, platform, or legal decisions and were not guessed at.

## Severity definitions

- **High:** Blocks a safe implementation choice or production claim until verified; does not imply an existing application defect.
- **Medium:** Material documentation clarification or an unresolved architecture/business decision that could cause inconsistent behavior if left implicit.
- **Low:** Non-blocking version/reference maintenance or future automation opportunity.

## Findings

### High — version and provider combination is not pinned

The skill repository contains no application package manifest, lockfile, Prisma schema, migrations, or configured database. Official Prisma Postgres documentation currently describes PostgreSQL 17 and PgBouncer pooling, with direct connections recommended for migration work. Current Prisma ORM documentation shows material changes between ORM 7 and the ORM 8 release-candidate documentation: migration application changes from `migrate deploy` to a plan/`db migrate` workflow, and the transaction API and isolation-level configuration differ. Better Auth's current Prisma adapter guide targets Prisma 7 and discusses Prisma 6 and earlier separately; the adapter package currently declares Prisma 5–7 peer dependencies, not Prisma 8.

**Impact:** Exact SAP Core build, migration, and adapter compatibility cannot be certified. Selecting Prisma ORM 8 without confirmed Better Auth adapter support would be an avoidable compatibility risk.  
**Disposition:** Record the evidence in [TECHNICAL_COMPATIBILITY_MATRIX.md](TECHNICAL_COMPATIBILITY_MATRIX.md). No version was selected or invented. Before application work, pin the Node.js, Next.js/React, Prisma CLI/client, Better Auth adapter, CASL, and database-platform versions together, then verify generated auth models, multi-schema migrations, and transaction behavior against that exact set.

### Medium — PostgreSQL constraint semantics needed more explicit guardrails

The PostgreSQL skill correctly recommends foreign keys, uniqueness, checks, and `NOT NULL`, but did not spell out three operational details: a PostgreSQL `CHECK` accepts `NULL`; a `CHECK` cannot guarantee an invariant involving other rows; and PostgreSQL does not automatically index the referencing side of a foreign key. Default unique semantics also treat nulls as distinct, which can leave a nullable natural key non-unique.

**Impact:** A reader could mistakenly rely on a row check for a cross-row invariant, assume a check disallows absent values, or assume a nullable unique key prevents duplicates. Missing child-side indexes can also make referential changes and joins slower.  
**Disposition:** Added concise, PostgreSQL-17-aligned guidance to `Database Engineering/postgresql-design/SKILL.md`. Cross-row journal/stock invariants remain correctly assigned to transaction/concurrency protocols in the existing database-integrity and domain guidance.

### Medium — cross-schema relationship policy remains a governance decision

PostgreSQL and Prisma's multi-schema documentation support schema-aware relations and cross-schema foreign keys for supported configurations. SAP Core's context intentionally allows such foreign keys only when supported and explicitly approved. That is a sound guardrail, but the repository does not yet decide which relationships should be database-enforced versus enforced through a module contract. Company/plant consistency, shared master-data references, and Better Auth model placement are representative cases.

**Impact:** Do not infer that a foreign key transfers write ownership, nor assume a module contract alone will protect every alternate write path.  
**Disposition:** Flagged for the architecture/data-governance decision log when the real Prisma schema is designed. No cross-schema policy was invented here.

### Medium — production email delivery remains an explicit dependency

Better Auth's official email/password guidance requires configured callbacks to send verification and password-reset messages when those flows are enabled. The repository correctly says external email delivery is deferred and must not be described as operational.

**Impact:** Production signup, verification, and recovery behavior cannot be claimed until a delivery mechanism, abuse controls, and operational ownership are approved.  
**Disposition:** Retained as an open decision; no provider was added or selected.

### Medium — ERP policy choices are intentionally unresolved

The skills consistently identify decisions such as tax treatment, currency and exchange-rate policy, fiscal calendars, inventory valuation and negative-stock rules, receipt/invoice tolerances, document-number gap rules, payroll/labor compliance, retention, and manufacturing costing/yield. These cannot be validated as universal defaults.

**Impact:** Technical schemas and sample lifecycle labels must not be mistaken for approved legal or accounting policy.  
**Disposition:** Detailed in [ERP_DOMAIN_AUDIT.md](ERP_DOMAIN_AUDIT.md); keep these questions with the responsible product, finance, operations, and legal owners.

### Medium — this audit does not establish application security or runtime behavior

The parent Sap Core folder has no application implementation or database to inspect. The 59 skills provide controls and review criteria; their presence is not evidence that runtime routes, roles, migrations, logs, exports, or database privileges implement those controls.

**Disposition:** Explicitly limited in [SECURITY_AUDIT.md](SECURITY_AUDIT.md). No application security test, migration, database change, build, or runtime test is claimed.

## Category-by-category review

- **Architecture & Engineering (9):** Modular-monolith boundaries, REST/server responsibilities, runtime validation, and state ownership are consistent. Vite+ remains an evaluation-only tool until a pinned compatibility task is demonstrated.
- **Database Engineering (7):** One PostgreSQL database and ten schema owners are represented consistently. Prisma multi-schema support is real for documented PostgreSQL configurations, but the exact application version/provider configuration is still unverified. Constraint, transaction, migration, and least-privilege guidance is generally sound.
- **ERP Business Domain (9):** Procurement, inventory, sales, finance, production, master-data, organization, and HR lifecycles preserve ownership and historical evidence. Product/legal details remain open by design.
- **Enterprise Engineering (9):** Better Auth identity/session responsibilities are distinct from CASL policy; authorization, transaction, concurrency, workflow, approval, audit, and numbering guidance is consistent. Adapter version compatibility remains a gate.
- **Frontend & Experience Engineering (15):** State/cache scope, bounded data access, input handling, file safety, accessibility, and package identity warnings are consistent. React PDF and XLSX package/edition decisions remain unresolved.
- **Quality & Security (10):** Threat, error, testing, review, performance, cache, observability, and release guidance is risk-based and consistently avoids claiming unrun checks.

## Verification performed

- Read all 59 `SKILL.md` files and the substantive supplemental reference, plus the root context, rules, decisions, workflow, index, dependency map, progress, first-pass consistency audit, and reference register.
- Search confirmed **59 `name:` frontmatter entries across 59 skill files**.
- Search confirmed **826 required-heading matches across 59 skill files (14 headings per file)**. This checks heading presence, not YAML validity or semantic completeness.
- Manually reviewed category ownership, schema names, module boundary statements, core database/ERP invariants, and the dependent-to-prerequisite dependency map. The map was not run through a graph tool during this pass.
- Consulted official PostgreSQL 17 constraints documentation; PostgreSQL current docs for numeric, date/time, transaction isolation, explicit locks, and sequences; Prisma multi-schema, migration, transaction, and Prisma Postgres documentation; Better Auth adapter, session, cookie, and email/password documentation; and CASL project documentation/source through Context7. The CASL hosted v6 guide itself remained unreadable to the text fetcher.
- Refreshed the technical reference register with the source claims and limitations.

## Not performed

No YAML parser, automated Markdown-link checker, graph-cycle program, build, lint, package install, typecheck, application test, Prisma schema validation, migration, database connection, deployment, penetration test, or runtime check was available or run. Search-based checks do not substitute for those validations. `TECHNICAL_REFERENCES.md` distinguishes checked documentation from pointers not opened.

## Recommended next phase

Before building ERP modules, establish the application repository's lockfile and supported runtime, select a Prisma/Better Auth adapter pair, and validate a minimal schema containing the ten namespaces plus auth-owned models. In a disposable PostgreSQL environment matching Prisma Postgres's documented engine major, test schema generation, cross-schema foreign keys, exact decimal and date mappings, migration review/application, concurrent posting/stock invariants, and pooled-versus-direct connection behavior. Record any policy decisions in the existing decision log; do not start external integrations or change the modular-monolith baseline as a side effect.
