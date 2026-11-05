---
name: tanstack-table
description: Defines efficient and correct SAP Core data-grid behavior with TanStack Table.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# TanStack Table

## Purpose
Present ERP records in configurable, accessible tables without loading unbounded data into the browser.

## Scope
Column definitions, stable row identity, server pagination/filter/sort, selection, visibility, and query integration.

## When to Use This Skill
Use for business lists, work queues, ledger views, and data-heavy reports.

## When NOT to Use This Skill
Do not use the table library as an excuse to fetch and client-process a full ERP table.

## SAP Core Context
TanStack Table manages table state/render models; TanStack Query fetches server pages; REST applies authorized sorting/filtering/pagination; TanStack Virtual is optional when rendering cost is measured.

## Core Principles
The server owns the authorized result set. Table state is a user request, never authority over which rows may be read.

## Mandatory Rules
- Use server-side pagination, filtering, and sorting for ERP-scale datasets.
- Use stable database IDs as row identifiers, not visible row indexes.
- Allowlist sortable/filterable fields and bound page size on the API.
- Make sort order deterministic with a unique tie-breaker; define consistency expectations for changing datasets.
- Provide accessible headers, sort state, selection labels, keyboard behavior, and empty/loading/error states.

## Architecture Guidelines
Keep column definitions typed and feature-owned. Normalize filters into URL/query state where shareable; connect state to TanStack Query keys. Distinguish page selection from “all matching records” semantics. Bulk actions must reauthorize every target server-side and recheck state. Virtualize only after measuring and keep table semantics/accessibility intact.

## Implementation Guidelines
**Example:** A vendor invoice queue requests 50 rows filtered by company/status and sorted by due date plus stable ID. The API rejects unknown sort fields and caps page size. Row selection submits selected IDs to a server command, which rechecks company and approval policy rather than trusting visible selection.

## Data and Security Considerations
Do not place sensitive columns in hidden client-side data “for convenience.” Server responses must be scoped; export/bulk routes enforce same policy. Avoid leaking total counts if they reveal protected records.

## Common Anti-Patterns
Fetch all then paginate; index row IDs; unbounded pageSize; client-only filtering of confidential rows; unstable sort; assuming selected rows remain authorized; table component calling Prisma.

## Validation Requirements
**Implementation checklist:** server modes; stable row IDs; URL/key synchronization; allowlisted query controls; deterministic sort; accessibility; bulk semantics. **Review checklist:** large dataset behavior; cross-company filter tampering; sort/pagination edge cases; row identity after sorting; selection across pages explicitly defined.

## Related Skills
tanstack-query, tanstack-virtual, api-engineering, performance-optimization, authorization-engineering.

## Reference Documents
TanStack Table official docs https://tanstack.com/table/latest/docs/introduction and manual server-side pagination https://tanstack.com/table/latest/docs/guide/pagination. Confirm options/APIs for the pinned version.
