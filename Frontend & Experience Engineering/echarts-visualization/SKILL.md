---
name: echarts-visualization
description: Builds accurate, accessible, bounded Apache ECharts visualizations for SAP Core reporting.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# ECharts Visualization

## Purpose
Turn authorized, validated report aggregates into charts that preserve business meaning.

## Scope
Chart selection, ECharts options/data, interactions, responsiveness, performance, and accessibility.

## When to Use This Skill
Use for dashboards, trends, distributions, and analytical views where a chart communicates better than a table.

## When NOT to Use This Skill
Do not use a chart as the only means to inspect or export exact financial/operational values.

## SAP Core Context
Apache ECharts renders UI visualization. The server owns aggregation and authorization; TanStack Query fetches report data. Charts do not calculate authoritative accounting values.

## Core Principles
Charts are projections of trusted server data. Label axes, units, dates, currency, filters, and missing-data semantics clearly.

## Mandatory Rules
- Aggregate and scope large datasets server-side; do not send full ERP ledgers to the browser for charting.
- Preserve exact source precision in report DTOs; presentation rounding is explicit and never used for posting.
- Provide accessible title/summary and a tabular or textual alternative for critical values.
- Bound points/series and dispose/update chart instances according to the official wrapper/library lifecycle for the pinned version.
- Treat user-controlled labels/content as untrusted text; avoid unsafe HTML renderers.

## Architecture Guidelines
Use dedicated report endpoints that enforce company, permission, period, and filter boundaries. Keep chart option construction separate from financial/business calculations. Select chart type that matches comparison/time/part-to-whole semantics; avoid misleading truncated axes. Use server pagination or sampling/aggregation for long time ranges. Define empty, loading, error, partial, and stale states.

## Implementation Guidelines
**Example:** A finance dashboard requests monthly debit/credit totals for an authorized company and period; server returns exact decimal strings plus currency and month keys. UI converts to display-safe numbers only for chart rendering, labels the currency/rounding, and links to a permission-checked table for exact ledger lines.

## Data and Security Considerations
Chart responses can reveal sensitive payroll, margin, or account data even if rows are hidden. Authorize report endpoint and export independently. Avoid embedding secrets or raw HTML in tooltip formatters.

## Common Anti-Patterns
Browser aggregation of full ledger; float chart result treated as accounting fact; unlabeled units; misleading axes; canvas-only accessibility; unrestricted tooltip HTML; excessive points causing memory spike.

## Validation Requirements
**Implementation checklist:** authorized aggregate contract; units/currency/timezone; point limit; accessible alternative; responsive and cleanup. **Review checklist:** aggregate totals reconcile; no cross-company leakage; screen reader/text alternative; large-period performance; empty/error behavior.

## Related Skills
tanstack-query, performance-optimization, caching-strategy, financial-accounting, api-engineering.

## Reference Documents
Apache ECharts official docs https://echarts.apache.org/en/option.html and accessibility docs https://echarts.apache.org/handbook/en/best-practices/aria/. Verify option/lifecycle APIs against the pinned ECharts release.
