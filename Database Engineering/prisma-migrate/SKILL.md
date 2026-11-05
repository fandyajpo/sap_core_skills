---
name: prisma-migrate
description: Plans safe Prisma Migrate schema evolution and rollout for SAP Core PostgreSQL.
category: Database Engineering
version: 1.0.0
status: active
---

# Prisma Migrate

## Purpose
Evolve production data structures predictably while preserving migration history and application availability.

## Scope
Schema changes, migration review, backfills, rollout sequencing, drift, and recovery planning.

## When to Use This Skill
Use for every change to persisted structure, constraint, index, schema mapping, or data transformation.

## When NOT to Use This Skill
Do not edit an already-applied migration to repair a production database or treat schema push/reset workflows as production migration strategy.

## SAP Core Context
Prisma Migrate is the migration system. Migrations target the `sap_core` PostgreSQL database and its ten schemas. Exact CLI commands differ across Prisma versions; use the pinned version's official deploy workflow.

## Core Principles
Migration files are ordered, reviewed artifacts. Safe rollout accounts for old and new app versions coexisting and for actual production volume.

## Mandatory Rules
- Never modify or delete a migration already applied to shared/staging/production databases; add a new forward migration.
- Separate schema expansion, data backfill, application cutover, and destructive contraction when compatibility requires it.
- Review generated SQL for locks, table rewrites, defaults, nullability, index build behavior, and multi-schema effects.
- Backfills must be bounded, restartable, observable, and safe to rerun.
- Do not run destructive resets or development-only migration commands against production.

## Architecture Guidelines
For a non-null column on a populated table, consider nullable/additive schema first, deploy code that writes/reads compatibly, backfill in controlled batches, validate completeness, then add a constraint in a later step. Coordinate migrations and app deployment; plan lock duration, storage, backup/recovery, and rollback/roll-forward. Prisma's official docs distinguish development migration workflows from deployment workflows; command names are version-specific and must not be copied blindly.

## Implementation Guidelines
**Example:** Add `organization_id` to an existing master-data table: additive nullable column and index; deploy code that writes it and temporarily handles legacy rows; run a scoped resumable backfill; verify no nulls and ownership correctness; add FK/not-null constraint in a later migration; remove compatibility only after all app instances are updated. Do not combine a large table rewrite and application behavior change into an unreviewed one-step migration.

## Data and Security Considerations
Run migration credentials with required but limited DDL scope, not normal request credentials. Never embed secrets in migration files or logs. Preserve finance/inventory history; schema cleanup must not erase posted facts. Test against representative data volume and PostgreSQL version.

## Common Anti-Patterns
Editing applied files; production reset; deploying app before compatible schema; blocking full-table backfill; assuming generated SQL is safe; one huge migration for unrelated modules; unrecoverable data transformation.

## Validation Requirements
**Implementation checklist:** migration reviewed; SQL/provider/schema mapping checked; old/new code compatibility; backfill restart and metrics; lock/data risk; backup and recovery path; exact version-specific deploy process. **Review checklist:** run migration checks in disposable/staging environment; test fresh database and upgrade path; inspect drift/history; confirm no secrets/destructive operation.

## Related Skills
prisma-orm, database-integrity, release-management, postgresql-design.

## Reference Documents
Prisma Migrate docs https://www.prisma.io/docs/orm/prisma-migrate and production deployment docs https://www.prisma.io/docs/orm/prisma-migrate/workflows/development-and-production. Prisma's official materials show workflow changes across versions (including recent major-version CLI changes); consult the documentation matching the exact pinned version. PostgreSQL locking: https://www.postgresql.org/docs/current/explicit-locking.html.
