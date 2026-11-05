---
name: sharp-image-processing
description: Processes SAP Core image uploads safely with Sharp under explicit resource and format limits.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Sharp Image Processing

## Purpose
Validate and transform image uploads without allowing untrusted files to exhaust resources or execute active content.

## Scope
Image intake, metadata, transformations, output formats, memory/CPU controls, and derived assets.

## When to Use This Skill
Use for company logos, product images, employee images, or other approved image workflows.

## When NOT to Use This Skill
Do not process arbitrary files as images or accept an upload because its extension/MIME header claims an image type.

## SAP Core Context
Sharp is the approved image processor. Upload storage/provider integration must follow actual project scope; no external storage service is presumed.

## Core Principles
Treat bytes as hostile; validate actual decoded content and bound work before producing an asset.

## Mandatory Rules
- Enforce request byte limit before decode; validate detected format, dimensions, pixel count, and permitted output formats.
- Reject unsupported/animated/multipage inputs unless explicitly designed and tested.
- Apply CPU, memory, concurrency, and timeout limits appropriate to deployment.
- Strip metadata such as GPS unless a business purpose requires it; re-encode safe output and never serve untrusted SVG as active content.
- Store generated assets with random server-controlled identifiers and authorization checks.

## Architecture Guidelines
Separate upload authorization from transformation. Normalize to a small approved raster format/size set; create thumbnails asynchronously only if needed. Avoid trusting client filenames or dimensions. Keep transformations deterministic and versioned so derived assets can be regenerated. Verify Sharp platform binaries and deployment runtime support for the pinned version.

## Implementation Guidelines
**Example:** A product-image endpoint accepts a bounded upload, inspects detected format and pixel dimensions, rejects excessive dimensions, rotates using metadata where appropriate, strips unneeded metadata, resizes to approved bounds, and stores a generated file tied to an authorized product. It does not preserve arbitrary original bytes for public serving by default.

## Data and Security Considerations
Guard against decompression bombs, polyglot files, path traversal, SVG script content, and denial of service. Do not process in a request path beyond safe time/resource budgets. Ensure file access remains scoped to company/product permissions.

## Common Anti-Patterns
Trust extension/MIME; decode huge image before limits; public original upload; retaining GPS; unbounded concurrent processing; serving raw SVG; user filename as path; exposing internal storage URL.

## Validation Requirements
**Implementation checklist:** auth; byte/dimension/pixel limits; detected format allowlist; transformation policy; metadata policy; output access; runtime limits. **Review checklist:** malformed/polyglot/oversized input; CPU/memory concurrency; output content-type; cross-company file access; cleanup on failure.

## Related Skills
application-security, performance-optimization, input-validation, human-resource-management.

## Reference Documents
Sharp official docs https://sharp.pixelplumbing.com/ and API docs https://sharp.pixelplumbing.com/api-constructor. Verify deployment/runtime constraints for the pinned release.
