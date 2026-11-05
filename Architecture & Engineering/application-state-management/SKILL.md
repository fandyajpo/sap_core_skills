---
name: application-state-management
description: Assigns each SAP Core UI state to its authoritative owner and lifecycle.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# Application State Management

## Purpose
Prevent stale, duplicated, or incorrectly persisted frontend state by assigning explicit ownership.

## Scope
Server state, shared client state, local state, form state, and URL/navigation state.

## When to Use This Skill
Use whenever data or UI state needs to be fetched, shared, cached, persisted, or synchronized.

## When NOT to Use This Skill
Do not introduce a global store for state confined to one component or for data already owned by a server-state cache.

## SAP Core Context
TanStack Query owns asynchronous server state and cache; Zustand owns shared client/UI state; React state owns isolated component state; React Hook Form owns form state; URL/search parameters own shareable navigation/filter state.

## Core Principles
Every state value has one source of truth, an explicit lifetime, and a defined invalidation/reset policy.

## Mandatory Rules
- Never mirror server records in Zustand merely for convenience.
- Do not put credentials, session secrets, or authoritative permissions in client state.
- Reset organization-specific UI/cache state when actor or company context changes.
- Keep shareable table filters and page state in URL when that is the intended user experience.

## Architecture Guidelines
Use Query for remote records and mutation lifecycle; use form state for unsaved edits; use local state for temporary disclosure/selection; use Zustand only where multiple distant components need transient client coordination. Define precedence when URL filters, defaults, and saved preferences overlap. Server authorization is authoritative regardless of cached UI hints.

## Implementation Guidelines
**Example:** A sales list's sort/filter/page lives in URL; TanStack Query keys include the normalized company scope and parameters; a mutation invalidates or updates the relevant query after server success. A dialog-open boolean stays local. Do not copy the fetched order list into a Zustand array.

## Data and Security Considerations
Cache keys must distinguish organization/user-scoped data where necessary. Invalidate on session/company changes and mutations affecting totals or permissions. Do not rely on client state for policy enforcement. Avoid persisting sensitive HR or finance data into local storage.

## Common Anti-Patterns
Two writable copies of a server record; Query result copied to Zustand; global form store; URL and component state disagreeing; stale data shown after company switch; state updated optimistically without rollback/authorization behavior.

## Validation Requirements
**Implementation checklist:** source of truth/lifetime/reset documented; server query key scoped; forms own drafts; URL reflects shareable state. **Review checklist:** no duplicate remote cache; context switch clears protected state; mutation failure restores UI; direct API still enforces permissions.

## Related Skills
react-engineering, tanstack-query, zustand-engineering, form-engineering, caching-strategy.

## Reference Documents
TanStack Query https://tanstack.com/query/latest/docs/framework/react/overview; Zustand https://zustand.docs.pmnd.rs/; React Hook Form https://react-hook-form.com/get-started. Check behavior against installed versions.
