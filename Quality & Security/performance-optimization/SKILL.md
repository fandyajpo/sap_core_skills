---
name: performance-optimization
description: Improves measured SAP Core database, server, browser, and file-processing performance without weakening integrity.
category: Quality & Security
version: 1.0.0
status: active
---

# Performance Optimization

## Purpose
Meet user-facing latency and resource budgets using measured evidence.

## Scope
Database queries, server rendering, browser rendering, file/report work, payloads, and caching interaction.

## When to Use This Skill
Use when a route or operation is slow, resource-heavy, or expected to process large ERP data.

## When NOT to Use This Skill
Do not optimize by removing authorization, constraints, audit, or exact business semantics.

## SAP Core Context
SAP Core must handle growing ERP records while preserving server-side authorization, database integrity, and accurate reports. No observability vendor or cache service is preselected.

## Core Principles
Profile a representative workload, identify the bottleneck, change one major factor, and measure again.

## Mandatory Rules
- Define a measurable latency/throughput/memory target and test environment.
- Page/filter/aggregate large data on the server; virtualize only the bounded rendered slice.
- Inspect query plans and avoid N+1/unnecessary fields.
- Bound image/XLSX/PDF processing time, bytes, rows, pixels, and concurrency.
- Do not cache protected data without a correct identity/company key and invalidation strategy.

## Architecture Guidelines
Measure end-to-end and component/database spans separately. Use realistic data volume/distribution and concurrency. Optimize query shape/indexes, payload, server/client boundary, or rendering based on evidence. Consider write amplification and lock duration when adding indexes or batch operations. Defer background queues/brokers unless synchronous limits are demonstrably insufficient and an approved architecture decision exists.

## Implementation Guidelines
**Example:** A slow inventory list is profiled: database plan shows a scan under company/status filter; the team tests a targeted composite index and bounded select, then measures latency/write overhead under representative reads/writes. It does not prefetch every stock row into Zustand.

## Data and Security Considerations
Performance instrumentation must redact sensitive values. Caches may leak cross-company data; batch endpoints need per-record authorization. Resource limits protect against both accidental load and denial of service.

## Common Anti-Patterns
Premature caching; indiscriminate indexes; measuring only local empty DB; reducing correctness checks; unbounded parallel image jobs; increasing page size to avoid pagination; tuning without before/after evidence.

## Validation Requirements
**Implementation checklist:** target/baseline; representative volume; bottleneck; measured change; regression/lock cost; cache/security review. **Review checklist:** correctness still holds; p95/throughput/resource metrics compared; large tenant included; no secret telemetry; rollback option.

## Related Skills
database-performance, tanstack-virtual, caching-strategy, observability, database-integrity.

## Reference Documents
PostgreSQL EXPLAIN https://www.postgresql.org/docs/current/using-explain.html; Next.js performance guidance https://nextjs.org/docs/app/guides/production-checklist; Sharp limits https://sharp.pixelplumbing.com/api-constructor. Version and workload specifics must be verified.
