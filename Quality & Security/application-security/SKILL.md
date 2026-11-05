---
name: application-security
description: Establishes threat-aware security controls across SAP Core routes, modules, files, and data.
category: Quality & Security
version: 1.0.0
status: active
---

# Application Security

## Purpose
Reduce the likelihood and impact of unauthorized access, data exposure, and business-process abuse.

## Scope
Threat modeling, authentication/authorization, input/output boundaries, secrets, files, dependencies, and security review.

## When to Use This Skill
Use for every feature; apply heightened review to identity, finance, inventory, HR, uploads, exports, and admin actions.

## When NOT to Use This Skill
Do not treat this checklist as a penetration test, compliance certification, or substitute for a concrete threat assessment.

## SAP Core Context
SAP Core is a server-backed ERP with company-scoped sensitive data, Better Auth sessions, CASL policies, Next.js, PostgreSQL, and no initial external integration platform.

## Core Principles
Assume requests, files, caches, and stored rich content can be hostile. Security controls must exist at the server boundary and preserve business invariants.

## Mandatory Rules
- Threat-model actor, asset, entry point, trust boundary, abuse case, and control for consequential changes.
- Validate sessions and enforce CASL/resource/company access on every protected server operation.
- Use least privilege, runtime input validation, parameterized queries, safe output encoding, and bounded resource use.
- Keep credentials/keys server-only; rotate/manage via approved secret storage and avoid logging.
- Treat uploads, spreadsheet cells, rich text, report filters, and export paths as untrusted.
- Defer third-party integrations; do not create outbound network paths or callbacks speculatively.

## Architecture Guidelines
Use defense in depth: route validation, application policy, module-scoped data access, and database constraints. Deny by default. Protect state-changing cookie-authenticated requests with the configured auth library's CSRF/origin mechanisms; verify exact behavior for pinned Better Auth/Next versions. Apply rate limiting/abuse controls through approved infrastructure when available; do not invent a provider. Ensure caches are scoped to identity/company or disabled for sensitive responses.

## Implementation Guidelines
**Example:** For an invoice export, server validates bounded filters, derives company context from a validated session, checks CASL access, queries finance's export contract, emits only allowed columns, neutralizes spreadsheet formulas, and audits the export. A client-side role flag is irrelevant to this decision.

## Data and Security Considerations
Minimize PII, protect HR/payroll and financial records, define retention, sanitize rich content, prevent IDOR/CSRF/XSS/SQL injection/SSRF/resource-exhaustion, and keep errors non-sensitive. Audit controls themselves must be access-restricted.

## Common Anti-Patterns
Frontend-only permission checks; client-supplied tenant authority; secret in source/client bundle; raw HTML; unsafe file parsers; unbounded report; open redirect; logs containing tokens; over-broad admin; security assertions without tests.

## Validation Requirements
**Implementation checklist:** threats/assets; session/policy; company scope; validation/encoding; resource limits; secrets/logs; error/cache policy; audit. **Review checklist:** unauthenticated, unauthorized, cross-company, replay, malformed, oversized, and sensitive-output cases; dependency/runtime configuration reviewed.

## Related Skills
authentication-engineering, authorization-engineering, database-security, input-validation, error-handling, caching-strategy.

## Reference Documents
OWASP ASVS https://owasp.org/www-project-application-security-verification-standard/ and Cheat Sheet Series https://cheatsheetseries.owasp.org/; Better Auth security-related docs https://www.better-auth.com/docs/concepts/cookies. Apply controls to the actual pinned stack and deployment.
