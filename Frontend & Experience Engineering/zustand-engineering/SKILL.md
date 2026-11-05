---
name: zustand-engineering
description: Restricts Zustand to deliberate shared client/UI state in SAP Core.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Zustand Engineering

## Purpose
Coordinate shared transient client state without duplicating server state or creating a hidden global domain layer.

## Scope
Store shape, selectors, actions, persistence, lifecycle, and state reset.

## When to Use This Skill
Use when UI state must be shared across distant components and is not remote data, form state, local state, or naturally shareable URL state.

## When NOT to Use This Skill
Do not use Zustand as the cache for users, orders, stock, finance records, or other server-owned data.

## SAP Core Context
Zustand is for shared client/UI state only. TanStack Query owns remote state; React Hook Form owns forms; React local state owns isolated interaction; URL owns shareable navigation state.

## Core Principles
Store only the minimum state needed to coordinate UI behavior; define reset and persistence semantics.

## Mandatory Rules
- Do not mirror TanStack Query records into Zustand without a documented, exceptional reason.
- Keep session credentials and authoritative permissions out of persistent browser storage.
- Reset organization-specific state when the selected company or authenticated user changes.
- Select narrow slices to avoid needless re-rendering and expose explicit actions rather than arbitrary mutations.

## Architecture Guidelines
Use a feature-scoped store when ownership is clear; avoid a single app-wide store of unrelated concerns. Persist only benign preferences (for example, a non-sensitive panel preference) after security review. Keep URL state as source of truth for shareable filters. Do not put domain invariants in client actions; server commands remain authoritative.

## Implementation Guidelines
**Example:** A navigation shell store tracks whether the mobile sidebar is open and the active client-only command palette query. The purchase-order list remains in Query; its filter remains in URL; draft form lines remain in React Hook Form.

## Data and Security Considerations
Local storage can be read by injected scripts and survive sign-out. Avoid persisting company financial/HR records, session tokens, or security policy. Clear scoped UI state on company switch to prevent stale cross-context display.

## Common Anti-Patterns
Global store for everything; duplicated Query cache; persisted session; component-only state in global store; too many subscriptions; permissive `setState` actions; assuming client role state is authoritative.

## Validation Requirements
**Implementation checklist:** use case justifies shared store; state classification; persistence/reset; selector granularity; no server data. **Review checklist:** company/session switch; sign-out; re-render behavior; persistence contents; API still rejects unauthorized actions.

## Related Skills
application-state-management, react-engineering, tanstack-query, caching-strategy.

## Reference Documents
Zustand official docs https://zustand.docs.pmnd.rs/ and guides https://zustand.docs.pmnd.rs/guides. Verify APIs and middleware behavior against the pinned version.
