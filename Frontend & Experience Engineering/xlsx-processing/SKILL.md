---
name: xlsx-processing
description: Defines safe, bounded XLSX import/export workflows for SAP Core business data.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# XLSX Processing

## Purpose
Support controlled spreadsheet exchange without trusting workbook structure or values.

## Scope
XLSX parsing, template/versioning, validation, import staging, export, formulas, and resource limits.

## When to Use This Skill
Use for approved master-data imports, operational exports, and user-requested spreadsheets.

## When NOT to Use This Skill
Do not use workbook contents as authoritative records or accept arbitrary macro-enabled files without an explicit security decision.

## SAP Core Context
XLSX is approved for file/data processing. Imports target module-owned commands and schemas; the browser never writes Prisma directly.

## Core Principles
A spreadsheet is untrusted input and a derived output. Validate every row and report errors before applying business transactions.

## Mandatory Rules
- Enforce compressed file-size, decompressed entry, row, column, sheet, and processing-time limits.
- Validate actual file/container structure and allowlisted workbook types; reject unexpected macros/embedded objects/features unless explicitly supported.
- Treat formulas and cached formula values as untrusted; define whether formulas are rejected or evaluated by a trusted process (never execute workbook formulas in application code).
- Prevent spreadsheet formula injection in exports by safely encoding/neutralizing untrusted values according to the target format and consumer behavior.
- Stage and validate import rows; apply through module-owned, authorized, idempotent commands with a transaction policy.

## Architecture Guidelines
Version import templates and define required headers, type conversions, locale/decimal/date handling, and duplicate policy. Provide row-level error feedback without revealing unauthorized records. Large imports should be bounded/batched with progress, retry, and partial-failure semantics documented. Exports reapply authorization and use server-side filtering; do not export client-hidden columns.

## Implementation Guidelines
**Example:** An item import parses only expected sheets/headers, treats each cell as unknown, validates SKU/UOM/company through Zod and master-data rules, detects duplicate business keys, and previews errors. Confirmed rows are applied through master-data's application service; a partial result reports exactly which rows committed and can safely resume.

## Data and Security Considerations
XLSX is a ZIP-based container: protect against zip bombs and parser resource exhaustion. Avoid formulas/macros, external links, hidden sheets, and PII leakage unless explicitly required. For CSV-compatible spreadsheet outputs, neutralize cells beginning with formula-triggering characters based on documented format handling; test with target spreadsheet clients.

## Common Anti-Patterns
Trusting extension; importing entire file into memory; formula values treated as trusted; inserting raw rows into Prisma; no row-level validation; no tenant scope; export of hidden sensitive fields; formula injection in generated workbook.

## Validation Requirements
**Implementation checklist:** type/size/resource limits; template version; cell coercion/date/decimal rules; authorization; staging/error report; transaction/idempotency; export formula safety. **Review checklist:** malformed/zip-bomb workbook; formulas/external links/macros; Unicode/locale/date edge cases; duplicate retry; cross-company import/export; memory limits.

## Related Skills
input-validation, application-security, database-integrity, database-security, master-data-management, performance-optimization.

## Reference Documents
SheetJS official docs https://docs.sheetjs.com/ and security guidance https://docs.sheetjs.com/docs/miscellany/security/. Verify the exact community/commercial package and APIs used; the stack label “XLSX” does not select an edition or API surface.
