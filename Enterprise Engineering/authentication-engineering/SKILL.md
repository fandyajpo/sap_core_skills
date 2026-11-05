---
name: authentication-engineering
description: Implements Better Auth identity and session flows securely within SAP Core's Next.js and Prisma boundaries.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Authentication Engineering

## Purpose
Establish and validate who is acting through a managed identity and session lifecycle.

## Scope
Registration, email/password, verification/reset flows, sessions, cookies, account lifecycle, Next.js integration, and Prisma adapter.

## When to Use This Skill
Use for login, sign-up, session validation/revocation, password, verification, or account changes.

## When NOT to Use This Skill
Do not use authentication as business authorization or implement an external identity provider/integration without an approved scope.

## SAP Core Context
Better Auth is the approved authentication library; Prisma ORM persists platform data. Current Better Auth docs describe cookie-based sessions with server validation, but defaults and integration APIs are version-specific. External email delivery is deferred; required verification/reset delivery needs an explicit approved delivery capability before production use.

## Core Principles
A session identifies an actor; it does not grant access to every company or document. Use Better Auth's supported mechanisms rather than implementing password/session cryptography.

## Mandatory Rules
- Validate the session on the server for every protected operation; a cookie's mere presence is not proof of a valid session.
- Set explicit policy for session lifetime, renewal, freshness for sensitive actions, revocation, and account deactivation; do not blindly inherit library defaults.
- Keep secrets and cookies server-only; require secure cookie attributes in production and review SameSite/origin/CSRF behavior.
- Use Better Auth's password hashing, password reset tokens, and verification flows; do not store raw passwords or invent token algorithms.
- Keep authentication tables under documented ownership and reconcile generated adapter schema changes with the governed ten-schema Prisma design.

## Architecture Guidelines
Use the official Next.js adapter/handler for the pinned release and correctly forward request headers/cookies where its docs require. The Prisma adapter's current guide distinguishes Prisma 7 setup from Prisma 6 and earlier; verify version/driver adapter setup. Review generated auth schema and migration as a code change; isolate its models according to the approved ownership decision without breaking adapter expectations. Session cookie caching changes revocation freshness; choose deliberately, especially for sensitive operations. Auth configuration should not embed business roles as the complete authorization system.

## Implementation Guidelines
**Example:** Sign-in creates a Better Auth session; each server command resolves that session into a minimal actor context and then asks CASL about the specific company/resource. A password reset uses a short-lived library-managed token, generic response behavior where supported, and a defined delivery callback. Until an email-delivery decision exists, do not claim production reset/verification messages are operational; use only an explicitly safe local development pathway.

## Data and Security Considerations
Protect cookies with HTTPS/Secure, HttpOnly, appropriate SameSite and narrow domain/path settings. Validate trusted origins and CSRF protections for cookie-authenticated state changes; do not disable protections to bypass a deployment mismatch. Prevent account enumeration, brute force, and open redirects through supported controls and review. Revoke sessions on account compromise/deactivation and define when password reset revokes sessions. Avoid recording credentials, reset tokens, or cookies in logs.

## Common Anti-Patterns
Cookie-presence-only authorization; custom password hashing; tokens in local storage; long-lived sessions with no revocation plan; assuming email delivery exists; mixing CASL abilities into session validity; generated schema applied without review; stateless sessions when immediate revocation is required.

## Validation Requirements
**Implementation checklist:** pinned Better Auth/Prisma/Next versions; adapter/schema diff reviewed; signup/login/verification/reset; session expiry/refresh/revoke; secure cookie and CSRF/origin; account lifecycle; no secrets in logs. **Review checklist:** invalid/expired/revoked session denied at route/use case; session revoked after chosen sensitive events; enumeration/redirect tests; Prisma client remains server-only; email-provider deferral is explicit.

## Related Skills
nextjs-engineering, prisma-orm, authorization-engineering, application-security, database-security, api-engineering.

## Reference Documents
Official Better Auth: Next.js https://www.better-auth.com/docs/integrations/next; Prisma adapter https://www.better-auth.com/docs/adapters/prisma; email/password https://www.better-auth.com/docs/authentication/email-password; session https://www.better-auth.com/docs/concepts/session-management; cookies https://www.better-auth.com/docs/concepts/cookies. These pages were consulted during generation; verify the pinned library versions before using options or method signatures.
