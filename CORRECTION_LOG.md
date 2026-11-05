# Second-Pass Correction Log

**Date:** 2026-11-05 UTC.  
**Scope:** Only evidence-based changes made after the second-pass audit. No database or SAP Core application code was changed.

| ID | Severity | Location | Change | Rationale / evidence |
|---|---|---|---|---|
| C-01 | Medium | `Database Engineering/postgresql-design/SKILL.md` | Added that `CHECK` passes on `TRUE` or `NULL` and is row-local; noted unique-key null semantics; clarified that referencing FK columns are not automatically indexed. | PostgreSQL 17 official constraints documentation states each behavior. These details prevent false assumptions in required-value, natural-key, cross-row, and index design. |
| C-02 | Documentation | `TECHNICAL_REFERENCES.md` | Refreshed checked source status and recorded Prisma Postgres PostgreSQL 17/PgBouncer/direct-migration details; Prisma 6.13 multi-schema GA evidence; Prisma ORM 7 versus ORM 8 RC migration/transaction differences; Better Auth adapter's documented Prisma 5–7 range; PostgreSQL 17 sources; CASL source-reader limitation. | Official documentation reviewed on the audit date. The matrix explicitly separates doc review from compatibility testing and keeps unpinned technologies unverified. |
| C-03 | Documentation | `README.md`, `GENERATION_PROGRESS.md` | Linked the second-pass deliverables and updated audit status/progress. | Makes the reports discoverable and records actual checks and limits without replacing the historical first-pass audit. |
| C-04 | Documentation | Six new audit deliverables | Created the second-pass audit, compatibility matrix, database architecture audit, security audit, ERP domain audit, and this log. | Requested outputs, classified findings, verified references, and unresolved decisions are recorded separately. |

## Deliberately not changed

- No package version, auth schema placement, cross-schema foreign-key policy, Vite+/Next.js integration, PDF package, XLSX edition, email provider, database configuration, or legal/accounting policy was selected without an owner decision.
- No other skill received a rewrite: the audit found the core transaction, posting, stock, authorization, lifecycle, file-safety, and release guidance was already appropriately scoped and version-cautious.
- `FINAL_CONSISTENCY_AUDIT.md` remains as the first-pass record; it was not silently rewritten or represented as a runtime audit.
- No `DECISION_LOG.md` architecture decision was added because the audit did not change an accepted architecture baseline.

## Verification limits

The edits were checked by re-reading the changed files and searching for skill count/headings and referenced source strings. No YAML parser, Markdown link checker, graph tool, build, test suite, Prisma validation, database migration, or runtime/security test was available or run. See `SECOND_PASS_AUDIT.md` for the full limits and [TECHNICAL_COMPATIBILITY_MATRIX.md](TECHNICAL_COMPATIBILITY_MATRIX.md) for gates.
