---
name: tiptap-editor
description: Uses Tiptap for bounded, schema-controlled rich-text fields in SAP Core.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Tiptap Editor

## Purpose
Capture formatted business notes where plain text is insufficient while keeping content constrained and safe.

## Scope
Editor schema/extensions, JSON persistence, validation, rendering, and collaboration boundaries.

## When to Use This Skill
Use for bounded notes/descriptions that require formatting and have a clear business owner.

## When NOT to Use This Skill
Do not use it for arbitrary executable HTML, full document management, or externally synchronized collaborative editing in the initial phase.

## SAP Core Context
Tiptap is the rich-text editor. External collaboration/sync is deferred. Plain text is preferred for simple fields.

## Core Principles
The editor schema is an allowlist, not a security sanitizer. Stored content remains untrusted when rendered elsewhere.

## Mandatory Rules
- Define a minimal approved node/mark/extension schema per field; do not enable arbitrary embeds/scripts.
- Validate persisted document shape and enforce size/depth limits server-side.
- Sanitize/escape generated HTML at the rendering boundary and ensure links use safe protocols.
- Keep editor state separate from posted business facts; freeze or version content when a document is finalized.
- Verify extension compatibility against the pinned Tiptap/ProseMirror packages.

## Architecture Guidelines
Prefer structured JSON content as the canonical editor value when supported by the selected setup, with controlled renderers for HTML/PDF/export. Store plain text separately only when search/report requirements justify it. Avoid storing both JSON and HTML as competing sources of truth. External image/file nodes need explicit upload authorization and resource limits; integration/collaboration services are out of scope.

## Implementation Guidelines
**Example:** An internal purchase-order note allows paragraphs, emphasis, lists, and links from an allowlisted schema. On save, Zod validates document shape/size; server stores the content under the PO's company scope. PDF generation renders through a safe renderer; it does not trust a client-supplied HTML string.

## Data and Security Considerations
Rich content is a stored XSS vector. Sanitize server-rendered output, validate link destinations, restrict image sources, and protect sensitive business notes. Avoid execution of embedded content and ensure audit/retention covers finalized document text.

## Common Anti-Patterns
Raw HTML trusted from database; unrestricted extensions; accepting arbitrary iframe/media; schema validation only in browser; separate canonical HTML and JSON; assuming editor sanitization makes downstream rendering safe.

## Validation Requirements
**Implementation checklist:** use case; minimal schema; server validation; size limits; safe rendering; link rules; finalization semantics. **Review checklist:** stored XSS payloads; unsafe URL; malformed/oversized JSON; PDF/export rendering; company access; extension compatibility.

## Related Skills
input-validation, application-security, react-engineering, react-pdf.

## Reference Documents
Tiptap official docs https://tiptap.dev/docs and security guide https://tiptap.dev/docs/guides/output-json-html. Verify extensions/rendering behavior for installed version.
