---
name: audit-trail
description: Captures privacy-conscious, attributable SAP Core business evidence for review and reconciliation.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Audit Trail

## Purpose
Make consequential business changes attributable and reconstructable without over-collecting sensitive data.

## Scope
Audit event content, append-oriented persistence, actor context, business history, access, and retention.

## When to Use This Skill
Use for financial/inventory postings, approvals, security grants, sensitive master data, and other policy-defined changes.

## When NOT to Use This Skill
Do not call ordinary application logs an audit ledger or claim tamper-proofness without concrete controls.

## SAP Core Context
`system.audit_logs` is the initial cross-cutting audit entity reference. Domain documents may also own lifecycle histories. Auditing must not bypass module ownership or external integrations.

## Core Principles
An audit event records who did what, to which business object, when, in what organization, with what outcome/reason. Minimize captured payloads.

## Mandatory Rules
- Define audited event classes and required evidence before implementation.
- Capture authenticated actor, target identifiers, company scope, action/outcome, timestamp, and correlation/request reference where available.
- For required evidence of a committed business change, write the audit record in the same transaction or define a reliable durable alternative.
- Do not log secrets, passwords, session tokens, full sensitive payloads, or unnecessary PII.
- Restrict audit read/export and define retention; do not silently edit/delete evidence.

## Architecture Guidelines
Use domain-specific history when users need business detail and a cross-cutting audit record for administrative/security trace. Record changed fields or a minimized before/after summary according to classification. Use append-only application behavior and database privileges where feasible, but state limits: an administrator with database control can alter rows unless stronger external controls exist. Correlation IDs support tracing but do not identify the actor by themselves.

## Implementation Guidelines
**Example:** Approving an order persists the approved transition and audit event together: actor ID, company, order ID/revision, action, result, timestamp, reason code, and request correlation. Do not store the full order JSON or credentials in the audit row. If transaction rollback occurs, no “approved” audit event is recorded.

## Data and Security Considerations
Treat audit logs as sensitive data. Protect reads, retention, exports, backups, and deletion. Avoid leaking private values through diffs. Synchronize timestamps and distinguish actor-provided reason from system-generated context. Never imply cryptographic immutability unless implemented and reviewed.

## Common Anti-Patterns
Audit event missing on failure/success; transaction data and evidence diverge; full database row snapshots forever; editable audit UI; logs used as sole proof; no access control; pseudonymous actor without resolvable identity policy.

## Validation Requirements
**Implementation checklist:** event scope/schema; actor/target/company/time; transaction relation; sensitive-field minimization; retention/access. **Review checklist:** successful change has evidence; rollback does not claim success; update/delete blocked by policy; export restricted; audit itself does not leak PII/secrets.

## Related Skills
database-integrity, application-security, authorization-engineering, financial-accounting, approval-management, observability.

## Reference Documents
SAP_CORE_CONTEXT.md; PostgreSQL privileges https://www.postgresql.org/docs/current/ddl-priv.html; `observability` for operational logs. Applicable legal retention requirements require product/legal review.
