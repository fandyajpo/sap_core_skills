---
name: react-engineering
description: Sets SAP Core component, composition, accessibility, and rendering conventions.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# React Engineering

## Purpose
Build predictable ERP interfaces from composable, accessible React components.

## Scope
Component responsibilities, props, rendering, event handling, composition, and lifecycle-safe UI behavior.

## When to Use This Skill
Use when creating or modifying any React component, screen, or interaction.

## When NOT to Use This Skill
Do not use a component to own database access, authorization decisions, accounting calculations, or cross-module business workflows.

## SAP Core Context
React is the UI library within Next.js. TanStack Query owns remote state, Zustand shared client state, React local state isolated view state, and React Hook Form form state.

## Core Principles
Components render declared state and emit user intent. Domain rules live in server-side use cases; reusable UI should express states, not reproduce business policy.

## Mandatory Rules
- Use semantic HTML, keyboard-operable controls, visible focus, and accessible names.
- Avoid duplicating remote data in component state or Zustand.
- Keep stable keys and stable callbacks only where they provide actual value; do not optimize without evidence.
- Respect loading, empty, error, disabled, and permission-hidden states distinctly.

## Architecture Guidelines
Compose screens from domain-neutral primitives and module-owned feature components. Keep data orchestration near route/feature boundaries. Define props as explicit contracts and avoid components that accept arbitrary untyped records. Separate visual rendering from form and query lifecycles when it improves testability.

## Implementation Guidelines
**Example:** A purchase-order line table receives typed display rows and callbacks; mutation and server validation remain in the feature/application boundary. A disabled “Post” action may explain why, but the server independently checks status and permission. Use React state for a one-off disclosure; use URL state for a shareable filter; use Query for fetched orders.

## Data and Security Considerations
Never assume hidden UI means protected data. Minimize client DTOs; avoid rendering sensitive HR/finance fields outside authorized surfaces. Escape/encode untrusted text in its rendering context and avoid unsafe HTML injection.

## Common Anti-Patterns
Giant screen components; effect-driven data fetching that duplicates Query; global store for server records; index keys for reorderable ERP rows; permission checks only in JSX; rendering raw rich text as HTML.

## Validation Requirements
**Implementation checklist:** typed props; semantic controls; explicit loading/empty/error states; correct state owner; stable entity keys. **Review checklist:** keyboard path, focus, reduced motion, sensitive-data visibility, and behavior on server denial checked; component story/test represents meaningful states.

## Related Skills
nextjs-engineering, typescript-engineering, application-state-management, tailwind-engineering, form-engineering.

## Reference Documents
Official React documentation: https://react.dev/learn and https://react.dev/reference/react. Verify APIs against the React version supported by the selected Next.js release.
