---
name: database-security
description: Protects SAP Core database access, credentials, and persisted sensitive data.
category: Database Engineering
version: 1.0.0
status: active
---

# Database Security

## Purpose
Limit database compromise impact and prevent unauthorized data access through application and operational paths.

## Scope
Credentials, connectivity, privileges, query safety, sensitive data, backups, and database-facing authorization controls.

## When to Use This Skill
Use when configuring connections/roles, storing sensitive data, exposing records, or changing database access paths.

## When NOT to Use This Skill
Do not claim database security is solved by ORM typing, a hidden UI, or an unverified row-level security assumption.

## SAP Core Context
Prisma Postgres hosts PostgreSQL; Prisma ORM is server-only. Better Auth supplies identity and CASL supplies application policy. Sensitive HR, finance, and auth records have distinct access needs.

## Core Principles
Least privilege, defense in depth, minimal exposure, and safe observability. Authentication and CASL checks remain mandatory regardless of database role design.

## Mandatory Rules
- Keep database credentials server-side, outside source control and client bundles; use the deployment's approved secret mechanism.
- Use parameterized ORM/raw queries; never concatenate untrusted SQL identifiers or values.
- Scope every protected query by company/organization and authorize the operation server-side.
- Minimize returned fields and classify sensitive data; define retention and backup access controls.
- Do not log credentials, tokens, full sensitive records, or raw connection strings.

## Architecture Guidelines
Use separate least-privilege identities for application runtime and migration administration where supported by deployment operations. Avoid sharing privileged DDL credentials with request handling. Evaluate TLS/network/access controls and backup restore access using Prisma Postgres's actual service configuration. PostgreSQL row-level security is not assumed; if deliberately adopted, account for transaction-local context, pool reuse, privileged bypass roles, and explicit tests.

## Implementation Guidelines
**Example:** An HR endpoint resolves the actor's session, checks a CASL ability for the specific employee and company, performs an allowlisted query through HR's module adapter, and returns only fields required by the view. A direct ID request from another company is denied even if its UUID is valid.

## Data and Security Considerations
Encrypt secrets at rest/in transit per platform capabilities; minimize sensitive fields and avoid storing secrets unless required. Hash passwords through Better Auth's supported password mechanism rather than custom code. Test backup restore and ensure audit data does not become a secondary PII leak.

## Common Anti-Patterns
Root DB credentials in runtime; credentials in `.env` committed; raw SQL string concatenation; broad “admin” role; relying on UUID unpredictability; trusting client company IDs; logging query bind values; claiming RLS without pool-aware verification.

## Validation Requirements
**Implementation checklist:** secret flow; least privilege; encrypted connection; actor/resource/company policy; parameterized query; field minimization; backup/log review. **Review checklist:** scan client bundle and logs for secrets; cross-company access tests; database-role audit; migration/runtime credentials separated; restore/access path reviewed.

## Related Skills
application-security, authorization-engineering, authentication-engineering, postgresql-design, prisma-orm, audit-trail.

## Reference Documents
PostgreSQL security https://www.postgresql.org/docs/current/security.html and row security https://www.postgresql.org/docs/current/ddl-rowsecurity.html; Prisma security guidance https://www.prisma.io/docs/orm/more/best-practices. Provider-specific controls require checking current Prisma Postgres docs and project configuration.
