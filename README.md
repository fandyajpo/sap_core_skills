# SAP Core Engineering Skills

A practical engineering knowledge base for AI agents and engineers building SAP Core, a modular-monolith ERP platform. These skills define enforceable boundaries, data-integrity rules, implementation patterns, and review checks; they are not generic tutorials or application source code.

## SAP Core in brief

SAP Core begins as one deployable Next.js application backed by one Prisma Postgres-managed PostgreSQL database named `sap_core`. Prisma ORM is the server-side data-access layer and Prisma Migrate is the migration system. The database is divided into ten logical schemas: `core`, `master_data`, `procurement`, `inventory`, `sales`, `finance`, `production`, `human_resources`, `integration`, and `system`. Authentication uses Better Auth; CASL expresses business authorization. The product and technology baseline is defined in [SAP_CORE_CONTEXT.md](SAP_CORE_CONTEXT.md).

## Repository structure

The six skill categories are `Architecture & Engineering`, `Database Engineering`, `ERP Business Domain`, `Enterprise Engineering`, `Frontend & Experience Engineering`, and `Quality & Security`. Each skill is a directory with a concise `SKILL.md` entry point; its practical example and implementation/review checklists are included there, with supplemental reference files only where deeper guidance is useful. Root documents establish shared context, non-negotiable rules, workflow, decisions, index, dependencies, and generation status.

## How AI agents should use the skills

1. Read [GLOBAL_RULES.md](GLOBAL_RULES.md) and the relevant portion of [SAP_CORE_CONTEXT.md](SAP_CORE_CONTEXT.md).
2. Find the affected skill(s) in [SKILL_INDEX.md](SKILL_INDEX.md); follow the direct dependencies in [SKILL_DEPENDENCY_MAP.md](SKILL_DEPENDENCY_MAP.md).
3. Read each selected `SKILL.md` first, including its inline example and implementation/review checklists; follow supplemental references where present. Treat mandatory rules and checklists as review criteria.
4. For cross-module work, identify data ownership and transaction boundaries before editing. Do not bypass module contracts to reach another module's tables.
5. Check version-sensitive framework and adapter details against the versions actually pinned by the application. A URL is a source pointer, not proof that an API is compatible with an unselected version.
6. Validate with the repository's actual checks; report checks that were not run rather than implying success.

## Maintenance

Update this knowledge base when an approved technology, module boundary, schema ownership rule, or business invariant changes. Record architecture changes in [DECISION_LOG.md](DECISION_LOG.md), update all affected skills and dependency links in the same change, and re-run the consistency checks in [FINAL_CONSISTENCY_AUDIT.md](FINAL_CONSISTENCY_AUDIT.md) and update [GENERATION_PROGRESS.md](GENERATION_PROGRESS.md). Never silently rewrite a decision or mark unverified version-specific guidance as verified.

## Status

The 59 requested skill entry points are generated. Per-skill completion and validation status are tracked in [GENERATION_PROGRESS.md](GENERATION_PROGRESS.md). The first-pass structure review is retained in [FINAL_CONSISTENCY_AUDIT.md](FINAL_CONSISTENCY_AUDIT.md); the deeper technical review and its findings are in [SECOND_PASS_AUDIT.md](SECOND_PASS_AUDIT.md), with supporting [compatibility](TECHNICAL_COMPATIBILITY_MATRIX.md), [database](DATABASE_ARCHITECTURE_AUDIT.md), [security](SECURITY_AUDIT.md), [ERP domain](ERP_DOMAIN_AUDIT.md), and [correction](CORRECTION_LOG.md) reports. External service integrations are deferred; the `integration` schema is reserved only.
