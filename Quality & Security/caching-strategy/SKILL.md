---
name: caching-strategy
description: Defines safe freshness, identity scoping, and invalidation for SAP Core caches.
category: Quality & Security
version: 1.0.0
status: active
---

# Caching Strategy

## Purpose
Reduce repeated work without serving stale or unauthorized ERP data.

## Scope
TanStack Query cache, Next.js/server cache, HTTP caching, invalidation, freshness, and cache partitioning.

## When to Use This Skill
Use when enabling or changing any client/server/HTTP cache or mutation invalidation policy.

## When NOT to Use This Skill
Do not cache protected records globally or treat caching as a substitute for authorization.

## SAP Core Context
TanStack Query owns client remote-state cache; Next.js may add server/render caches depending on version. The deployed Next.js caching model must be checked for the pinned release.

## Core Principles
Cache key includes all result-affecting inputs; sensitive data has a short/explicit lifetime or no shared cache; every mutation has an invalidation policy.

## Mandatory Rules
- Never share authenticated company/user data across principals through an unscoped cache key.
- Review HTTP and framework cache headers for every protected response.
- Invalidate/refetch after stock, finance, approval, master-data, or permission changes that affect cached views.
- Clear or partition caches on sign-out, account change, and company-context switch.
- Verify Next.js cache directives/defaults against the pinned version; do not rely on assumed defaults.

## Architecture Guidelines
Document each cache's owner, key, freshness, stale-serving tolerance, and invalidation trigger. Prefer no-store or private behavior for highly sensitive data unless a verified scoped cache is necessary. Public/static reference data may be cached only if it is not company-sensitive and update cadence is defined. Avoid duplicate caches across server and client without a consistency policy.

## Implementation Guidelines
**Example:** The inventory balance query key includes company, plant, location, item filters and page. Posting a stock movement invalidates the affected balance summary and movement list. A user switching companies cannot see the previous company's cached response, even if the UI route is unchanged.

## Data and Security Considerations
Cache correctness is an authorization control. Avoid persisting PII/finance/HR data in browser storage. Be careful with server-rendered HTML/Flight payload caches and shared CDN layers. Do not include secrets in cache keys/logs.

## Common Anti-Patterns
Global query key; caching session response indefinitely; Next.js cache behavior assumed; only invalidating one list after mutation; stale org context; caching errors containing sensitive data; duplicated Query+Zustand copies.

## Validation Requirements
**Implementation checklist:** cache layer/key; identity/company scope; TTL/stale policy; mutation invalidation; logout/context reset; HTTP headers. **Review checklist:** two-user/two-company test; data mutation propagation; direct response headers; pinned framework behavior verified; sensitive cache persistence absent.

## Related Skills
tanstack-query, nextjs-engineering, authorization-engineering, application-state-management, performance-optimization.

## Reference Documents
Next.js caching docs https://nextjs.org/docs/app/guides/caching; TanStack Query https://tanstack.com/query/latest/docs/framework/react/guides/caching. Both are version-sensitive: verify actual pinned framework/library semantics.
