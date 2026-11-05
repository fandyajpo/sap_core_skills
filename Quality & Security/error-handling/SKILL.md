---
name: error-handling
description: Provides stable, safe failure behavior across SAP Core REST and business operations.
category: Quality & Security
version: 1.0.0
status: active
---

# Error Handling

## Purpose
Make failures understandable and recoverable without leaking implementation details or hiding partial effects.

## Scope
Domain errors, HTTP mapping, validation failures, transaction conflicts, logging, and user feedback.

## When to Use This Skill
Use for every API/use-case failure path and every operation with retry or side effects.

## When NOT to Use This Skill
Do not expose stack traces/SQL details to users or convert all errors into success-shaped responses.

## SAP Core Context
Internal REST and transaction-safe ERP operations require predictable errors. Logs are operational diagnostics; audit history is separate evidence.

## Core Principles
Classify errors by actionability and commit status. A client must know whether it can correct input, retry safely, reauthenticate, or stop.

## Mandatory Rules
- Map known validation, authentication, authorization, not-found, conflict, and transient failures to stable safe responses.
- Do not leak SQL, stack traces, secrets, session tokens, internal paths, or unauthorized resource existence.
- Do not retry a mutation unless idempotency and commit-outcome semantics are defined.
- Preserve the distinction between rolled-back failure and unknown/committed outcome.
- Attach a correlation/request identifier to internal diagnostics when available; never treat it as authorization.

## Architecture Guidelines
Use typed domain/application errors and a single REST mapping policy, but avoid one generic error that erases domain meaning. Validation messages should identify safe fields. For stale versions/stock conflicts, return a conflict that directs reload or resolution. For database constraint errors, map only recognized constraints; unknown failures remain server errors. User messages are localized/presentational; logs retain structured diagnostic context with redaction.

## Implementation Guidelines
**Example:** A repeated stock reservation returns a domain conflict with a safe message and correlation ID; it is not mislabeled as a generic server error, and the server does not retry the command without its idempotency key. A database stack trace remains in restricted logs, not the HTTP body.

## Data and Security Considerations
Redact PII, credentials, query parameters, and payloads. Avoid logging full request bodies. Error responses must not reveal cross-company record existence. Ensure alerting does not turn validation errors into noisy incidents.

## Common Anti-Patterns
Catch-all returns 200; raw Prisma message exposed; retries on every error; errors swallowed after partial writes; errors used as audit; user receives internal exception class; logs include secrets.

## Validation Requirements
**Implementation checklist:** error taxonomy; HTTP mapping; commit/retry semantics; safe user copy; correlation; redaction. **Review checklist:** unauthorized/not-found behavior; unique/serialization conflict; rollback; unknown exception; no sensitive leakage; client recovery path.

## Related Skills
api-engineering, application-security, observability, transaction-integrity, input-validation.

## Reference Documents
RFC 9457 Problem Details https://www.rfc-editor.org/rfc/rfc9457 and HTTP semantics RFC 9110 https://www.rfc-editor.org/rfc/rfc9110. Use the project's documented response envelope and avoid copying examples that don't match it.
