---
name: nextjs-engineering
description: Applies Next.js server and browser boundaries to SAP Core without moving domain policy into framework code.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# Next.js Engineering

## Purpose
Use Next.js as the web application framework while preserving explicit server/client and module boundaries.

## Scope
Routes, rendering, request handlers, server-only data access, navigation, and framework integration.

## When to Use This Skill
Use for route, page, handler, rendering, caching, or Next.js configuration changes.

## When NOT to Use This Skill
Do not put domain logic in framework entry points or use a browser component for privileged database access.

## SAP Core Context
Next.js is the approved application framework. Prisma Client, credentials, session validation, and protected use cases are server-only. The exact routing/rendering model must match the pinned Next.js version and app structure.

## Core Principles
Treat client/server boundaries as security boundaries. Framework adapters translate transport concerns; application services enforce business behavior.

## Mandatory Rules
- Never import Prisma Client or server secrets into a client bundle.
- Authenticate and authorize every protected server operation, not only page navigation.
- Validate request data at runtime; TypeScript annotations alone are insufficient.
- Verify cache behavior for user- and organization-scoped content before enabling shared caching.

## Architecture Guidelines
Keep route/page code thin: parse transport, establish actor context, invoke an application use case, and map results/errors. Use server rendering for data that must remain private; expose only minimal serializable DTOs to client components. Keep REST contracts explicit. Do not assume a particular Server Action, cache, middleware, or route API until confirmed against the project's pinned version.

## Implementation Guidelines
**Example:** A sales-order page obtains an authorized, company-scoped DTO on the server; interactive line editing is a client component that submits a validated request to the internal API. The server rechecks authorization and state before mutation. Do not treat the earlier page check as a durable grant. Use framework-native build/deploy tooling unless a Vite+ task has proven compatibility.

## Data and Security Considerations
Prevent IDOR by scoping lookup and authorization to the actor's company. Avoid caching authenticated data across users. Do not serialize raw ORM objects, session tokens, or internal error details. Use CSRF defenses appropriate to the actual authentication and request mechanism.

## Common Anti-Patterns
Fat route handlers; client-side authorization as enforcement; importing server-only modules into client components; broad cache keys; assuming middleware alone authorizes a document; undocumented use of framework caching.

## Validation Requirements
**Implementation checklist:** route contract and version API checked; runtime validation; server auth+CASL policy; company-scoped query; safe DTO; cache reviewed. **Review checklist:** test unauthenticated, forbidden, cross-company, stale-state, validation, and server-error paths; ensure no secret/server module reaches client graph.

## Related Skills
react-engineering, typescript-engineering, api-engineering, authentication-engineering, caching-strategy.

## Reference Documents
Official docs: https://nextjs.org/docs and https://react.dev/learn. Consult the matching Next.js version and the project's configured router; do not infer compatibility with Vite+.
