---
name: tanstack-virtual
description: Uses TanStack Virtual to bound DOM work for measured large SAP Core lists and tables.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# TanStack Virtual

## Purpose
Render only the visible portion of long collections when DOM/render cost is demonstrated.

## Scope
Virtual rows/columns, measured sizes, scrolling, stable item keys, and table integration.

## When to Use This Skill
Use for large result pages after profiling shows rendering/DOM cost is material.

## When NOT to Use This Skill
Do not use virtualization to justify downloading an entire ERP dataset or when a short accessible list is simpler.

## SAP Core Context
TanStack Virtual bounds rendered DOM; REST and TanStack Query still own server pagination and remote data. Virtualization is an optimization, not data access control.

## Core Principles
Bound both data volume and rendered elements; keep identity and keyboard/scroll behavior stable as rows are recycled.

## Mandatory Rules
- Paginate/filter/sort on the server; do not fetch the full table for virtualization.
- Use stable item keys based on row identity, not transient index when rows can reorder.
- Test focus, screen-reader semantics, sticky headers, dynamic row heights, and scroll restoration.
- Measure before adding complexity and compare real target devices/data volume.

## Architecture Guidelines
Virtualize within a page of server data or a controlled infinite-query window. Avoid rendering interactive controls that lose focus/state when an item is recycled. Ensure total/estimated size and measured size logic match actual rows. In grids, coordinate horizontal/vertical virtualization only when required; support fallback on printing/export where all rows are needed through a separate authorized server path.

## Implementation Guidelines
**Example:** An inventory movement view fetches a bounded page of authorized rows and virtualizes visible row rendering; moving to the next cursor fetches more. An export request sends filters to a server-side report function rather than asking the virtualizer for “all rows.”

## Data and Security Considerations
Virtualized-out content may remain in client memory; do not fetch unauthorized records. Avoid exposing sensitive values in hidden DOM or state. Recheck access on each page request and on exports.

## Common Anti-Patterns
Virtualize all data after unbounded fetch; index-based keys; inaccessible tab order; focus lost on recycle; printing only visible rows; unmeasured virtualization of small lists.

## Validation Requirements
**Implementation checklist:** profile; page/window bound; stable keys; row sizing; focus/accessibility; print/export path. **Review checklist:** scrolling at large size, sort/filter changes, keyboard navigation, screen reader, company switch, and memory/performance checked.

## Related Skills
tanstack-table, tanstack-query, performance-optimization, api-engineering.

## Reference Documents
TanStack Virtual official docs https://tanstack.com/virtual/latest/docs/introduction. Verify adapter and sizing APIs for the pinned version.
