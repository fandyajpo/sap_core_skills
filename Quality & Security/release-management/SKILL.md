---
name: release-management
description: Coordinates safe SAP Core application and database releases with compatible migrations and observable recovery.
category: Quality & Security
version: 1.0.0
status: active
---

# Release Management

## Purpose
Release application and schema changes in a controlled order with known compatibility and recovery paths.

## Scope
Build/test gates, migration order, rollout compatibility, release notes, rollback/roll-forward, and post-release monitoring.

## When to Use This Skill
Use for application, database, authentication configuration, or dependency releases.

## When NOT to Use This Skill
Do not assume code rollback reverses a database migration or that a destructive migration can be undone safely.

## SAP Core Context
Prisma Migrate manages PostgreSQL evolution; the exact command/CI sequence is version-specific. The modular monolith initially deploys as one application. No external integration deployment is in scope.

## Core Principles
Schema and app must be compatible during deployment. Prefer additive/expand-and-contract migrations and roll-forward data repair over unsafe destructive rollback.

## Mandatory Rules
- Pin and review dependency/framework versions; test supported migration and build workflow in CI/staging.
- Review migration SQL, lock duration, backfill, data volume, and production compatibility before release.
- Treat applied migrations as immutable; do not edit production history.
- Define release sequence, recovery owner, monitoring signals, and rollback/roll-forward conditions.
- Never run destructive production reset commands or expose credentials in logs.

## Architecture Guidelines
Use additive schema changes, deploy code compatible with old/new shape, backfill in bounded batches, validate, then remove obsolete fields after old code is gone. Separate long backfills from schema locks. Coordinate auth/session changes with cookie/domain policy and user impact. Keep release gates tied to actual checks; record skipped/unavailable checks honestly. Maintain a forward repair plan for data transformations.

## Implementation Guidelines
**Example:** Release a new required sales-order column by adding it nullable, deploying dual-compatible code, backfilling, verifying completeness, then applying not-null constraint and later removing compatibility code. If application rollback occurs, the additive column remains safe; do not attempt to “undo” production data by editing an applied migration.

## Data and Security Considerations
Use least-privileged migration credentials and approved secret management. Back up/restore strategy and recovery point must be understood for risky changes. Monitor authorization errors, transaction failures, latency, and reconciliation after release without logging sensitive payloads.

## Common Anti-Patterns
App deploy before incompatible schema; destructive rename/drop in one step; migration edited post-apply; no realistic data test; rollback assumed to restore data; release reports claim checks not run; secret in deployment logs.

## Validation Requirements
**Implementation checklist:** pinned version; checks; compatible migration sequence; data backup/backfill; rollout/rollback/forward repair; monitoring; owner. **Review checklist:** fresh and upgrade path; old/new app compatibility; production SQL/lock review; post-release reconciliation; actual CI status and known limitations.

## Related Skills
prisma-migrate, testing-strategy, observability, code-review, database-integrity.

## Reference Documents
Prisma Migrate workflow https://www.prisma.io/docs/orm/prisma-migrate/workflows/development-and-production; PostgreSQL locking https://www.postgresql.org/docs/current/explicit-locking.html. Verify migration commands and deployment process against the pinned Prisma version and platform.
