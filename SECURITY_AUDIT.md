# Security Audit

**Date:** 2026-11-05 UTC.  
**Scope:** Security guidance in the skill repository. This project contains no SAP Core application source, configured auth instance, database, or deployment to test. The findings below assess guidance and known dependencies only; they do not certify an implementation or compliance posture.

## Overall assessment

Security principles are consistently represented across the relevant skills: server-side session validation; CASL checks at protected operation boundaries; company/resource-scoped queries; least data returned; runtime validation; safe error/log behavior; file and rich-content controls; cache partitioning; and authorization tests. Better Auth is not treated as the business permission system, client visibility is not treated as access control, and audit logs are not called tamper-proof.

No runtime control can be confirmed from this repository. A complete application security review remains pending the application source, package lockfile, platform configuration, and data model.

## Findings

### High — authentication adapter compatibility is an implementation gate

The current Better Auth Prisma adapter guide is written for Prisma 7 and distinguishes Prisma 6 and earlier; the adapter's current peer-dependency metadata lists Prisma 5–7 but not Prisma 8. Prisma ORM 8 is documented as a release candidate, with a changed client and migration/transaction surface. The application has no locked versions.

**Required:** pin a supported adapter/ORM pair before implementing authentication. If Prisma 8 is considered, obtain an updated support declaration or validate the exact adapter/ORM pair in a test application before selecting it. Review generated auth model names, columns, relations, and schema placement as a migration, not as automatic output to apply.

### Medium — session-cache policy must be explicit

Better Auth documents a cookie-based session and optional short-lived cookie cache. With cookie caching enabled, a revoked session can remain usable on a device until cache expiry unless the request bypasses the cache or the chosen policy otherwise checks revocation. Current default session duration/freshness values are library defaults, not SAP Core security requirements.

**Required:** set explicit lifetime, refresh, freshness, revocation, account-deactivation, password-reset, and high-risk-operation policy. Keep cookie cache disabled or deliberately short/bypassed where the approved revocation requirement demands immediate checks. Verify `Secure`, `HttpOnly`, `SameSite`, trusted origins, proxy headers, and CSRF/origin behavior in the actual deployment.

### Medium — production verification and password recovery need approved delivery

The official Better Auth email/password documentation requires send callbacks for verification and reset messages. The repository correctly leaves external email delivery deferred. An enabled endpoint without a configured, monitored delivery route would not provide a complete recovery/verification service.

**Required:** product and operations must approve provider/callback ownership, delivery failure behavior, abuse controls, enumeration-safe responses, token handling, and incident monitoring before claiming these flows are production-ready.

### Medium — cross-schema and authorization placement need a real schema review

The repository correctly separates identity, roles, and authorization responsibilities, but the location of Better Auth-owned models and the mechanism enforcing schema/company relationship integrity are not yet decided. A database foreign key may preserve relationship integrity but does not authorize a caller or transfer module write ownership.

**Required:** document auth-model ownership and cross-schema relationship policy; verify organization scope both in the query and business policy. Test privilege changes and revocation against cached abilities/session data.

### Medium — no implementation-level security evidence exists

No route, Prisma query, migration, database role, cookie configuration, export route, storage path, logging pipeline, or dependency lockfile could be inspected. Controls in `GLOBAL_RULES.md` and skills are requirements, not evidence of enforcement.

**Required:** once code exists, inspect authentication and authorization at every server boundary, database privileges and connection strings, output DTOs, cache/HTTP headers, sensitive logs, HR/finance exports, upload/parser limits, and authorization-denial behavior. Run actual security tests and record exact results; do not infer security from UUIDs or ORM typing.

## Control coverage in the guidance

| Area | Guidance coverage | Evidence still required in implementation |
|---|---|---|
| Authentication/session | Better Auth session validation, cookie attributes, session lifecycle, reset/verification, revocation freshness are addressed. | Pinned adapter/runtime, actual cookie/origin config, session tests, delivery callback and failure behavior. |
| Authorization/RBAC | CASL policies are server-side, scoped by trusted actor/company/resource; membership is distinct from permission; UI gates are presentation only. | Policy factory, query filters, deny matrix, stale-membership behavior, object-level/field-level checks and export checks. |
| Database/data access | Server-only ORM, parameterized SQL, least-privilege identities, scoped reads, minimal DTOs, pooling/RLS caution. | Runtime-vs-migration identities, provider connection config, permissions, any RLS transaction-local context under PgBouncer, secret handling. |
| Audit and observability | Audit evidence is separate from operational logs, append-oriented, minimized, access-controlled; no unsupported immutability claims. | Concrete audit events/retention/access, redaction tests, database privilege and backup controls. |
| Files and rich text | Upload bytes/content and resource limits, spreadsheet formula safety, rich-text validation/rendering and XSS controls are specified. | Exact parser/edition/runtime, deployed limits, authorized storage, XSS/formula-injection regression tests, safe PDF/export paths. |
| Caching and client state | Scoped cache keys, invalidation, company/sign-out clearing, no persistent credentials or authority are explicit. | HTTP headers, Next.js cache behavior for pinned version, two-user/two-company tests, real sign-out/context-switch behavior. |
| HR/finance privacy | PII minimization, restricted exports/logs, retention and sensitive-report controls are called out. | Data classification, jurisdiction/legal basis, retention schedules, access review and actual export-field controls. |

## Product and legal decisions still open

- Email service and sender/domain policy, password recovery service levels, and user-verification requirements.
- Session lifetime, MFA or step-up requirements, account deactivation, session revocation, and audit retention.
- Identity, privacy, HR/payroll, financial, tax, and records-retention obligations by launch jurisdiction.
- Audit retention and administrative/database-operator threat model; no tamper-proofness is implied.
- Whether database row-level security is required; it is not assumed by the current baseline and must be tested with pooled connections if selected.

## Reference checks

Official Better Auth Prisma adapter, session management, cookies, and email/password pages were reviewed on 2026-11-05 UTC. CASL project documentation was retrieved through Context7 from the project repository; its hosted v6 guide did not render readable text in the page reader. Package versions are unpinned. See [TECHNICAL_COMPATIBILITY_MATRIX.md](TECHNICAL_COMPATIBILITY_MATRIX.md) for exact version limits and [TECHNICAL_REFERENCES.md](TECHNICAL_REFERENCES.md) for source status.
