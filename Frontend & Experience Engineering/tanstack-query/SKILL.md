---
name: tanstack-query
description: Owns SAP Core remote state, query caching, mutation lifecycle, and invalidation with TanStack Query.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# TanStack Query

## Purpose
Manage asynchronous server data as a cache with explicit identity, freshness, and mutation effects.

## Scope
Queries, keys, loading/errors, mutations, invalidation, optimistic updates, and server pagination integration.

## When to Use This Skill
Use for data fetched from SAP Core REST endpoints and their mutations.

## When NOT to Use This Skill
Do not use it for purely local UI state, form drafts, or authoritative authorization.

## SAP Core Context
TanStack Query owns remote state. Zustand must not mirror query data by default. Query data is a cache, not a permission grant or source of truth.

## Core Principles
Cache key identity must match the resource and all inputs that affect its result; mutation behavior must leave cache consistent with server truth.

## Mandatory Rules
- Include normalized filters, sort, page/cursor, and relevant company/actor scope in query identity.
- Validate/authorize on the server on every request; stale cache must never grant access.
- Invalidate or update all affected query families after a successful mutation and clear scoped caches on account/company change.
- Bound retries and do not automatically retry non-idempotent mutations without an idempotency contract.

## Architecture Guidelines
Centralize stable key factories and typed endpoint functions. Use query status for loading/empty/error states. Prefer invalidation/refetch for complex ledger mutations; optimistic updates only where rollback and reconciliation are straightforward. Avoid caching sensitive data persistently unless specifically reviewed. Tune stale/gc/retry behavior based on product semantics and pinned Query version, not copied defaults.

## Implementation Guidelines
**Example:** Sales order list key includes selected company, normalized status filter, sort and page. A successful order update invalidates the detail and relevant list/summary queries. A failed stock reservation does not leave an optimistic available-quantity decrease committed in the cache.

## Data and Security Considerations
Never put session tokens in query keys. Do not use a shared cache key for company-scoped data. Ensure server responses include only authorized DTO fields. On sign-out or tenant switch, remove protected cache entries according to app policy.

## Common Anti-Patterns
Global constant key for filtered lists; query results copied into Zustand; unscoped cached finance data; infinite retries; optimistic financial posting; stale permissions assumed from cached user object.

## Validation Requirements
**Implementation checklist:** stable key; scope/filters; cancellation/retry; invalidation; sensitive cache policy; query states. **Review checklist:** user/company switch cache reset; mutation failure; stale detail/list; duplicate mutation; server authorization independent.

## Related Skills
application-state-management, api-engineering, tanstack-table, caching-strategy, error-handling.

## Reference Documents
TanStack Query official docs https://tanstack.com/query/latest/docs/framework/react/overview and query keys https://tanstack.com/query/latest/docs/framework/react/guides/query-keys. Verify APIs/defaults against the pinned version.
