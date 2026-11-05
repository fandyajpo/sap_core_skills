---
name: react-pdf
description: Generates or displays SAP Core PDFs with bounded rendering and controlled business data.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# React PDF

## Purpose
Produce or preview business documents as PDF without exposing unauthorized data or exhausting server/browser resources.

## Scope
PDF generation/rendering selection, data input, layout, performance, output lifecycle, and version verification.

## When to Use This Skill
Use for printable business documents, reports, or controlled document preview.

## When NOT to Use This Skill
Do not use PDF as the authoritative accounting record or assume “React PDF” identifies one package unambiguously.

## SAP Core Context
The approved stack says React PDF, but the intended package must be confirmed: `react-pdf.org` documents the React PDF renderer, while similarly named viewer packages exist. The chosen package/version and server/client rendering responsibilities must be recorded; do not add both by assumption.

## Core Principles
A PDF is a derived representation of an authorized, versioned business document—not a mutable source of truth.

## Mandatory Rules
- Resolve which PDF package and task are intended before coding; verify its official docs and runtime support.
- Authorize source data on the server and pass a minimal DTO to rendering.
- Bound report rows, image sizes, concurrent jobs, memory, and execution time.
- Preserve document version, currency, date, and localization context; do not recalculate financial facts with floating point.
- Test page breaks, fonts, long labels, and generated content in the actual runtime.

## Architecture Guidelines
For a finalized invoice/PO, render from an immutable document snapshot or versioned data according to product policy. For large reports, generate in a controlled server job/streaming path only if supported and needed; avoid blocking a request with unbounded work. Viewer components should not fetch privileged documents without authorization. Cache keys include document version and access scope.

## Implementation Guidelines
**Example:** An authorized invoice endpoint loads a posted invoice DTO with exact decimal strings, currency, company address snapshot, and posting date. The PDF renderer converts values only for display and produces a versioned artifact. A correction creates a new document/version rather than silently replacing the old posted PDF.

## Data and Security Considerations
Do not expose confidential report PDFs through guessable public URLs. Validate embedded images/fonts/content, avoid remote resource fetching unless specifically controlled, and remove temporary artifacts safely. Ensure text/HTML from user notes is sanitized before rendering.

## Common Anti-Patterns
Confusing viewer and renderer packages; rendering directly from raw Prisma models; unbounded whole-ledger PDF; floating-point financial recomputation; public file URLs; trusting remote HTML/images; rendering posted docs from mutable current master data.

## Validation Requirements
**Implementation checklist:** package identity/version; data authorization; immutable source; limits; safe assets; layout/localization; access and retention. **Review checklist:** large file/memory; unauthorized download; page-boundary values; cross-company data; formula/totals match; final output reproducible.

## Related Skills
performance-optimization, application-security, financial-accounting, tiptap-editor, caching-strategy.

## Reference Documents
React PDF renderer documentation https://react-pdf.org/. Confirm whether the renderer or a separate PDF viewer is intended, and verify APIs/runtime compatibility for the pinned package.
