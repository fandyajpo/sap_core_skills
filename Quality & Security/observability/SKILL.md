---
name: observability
description: Provides privacy-safe operational visibility into SAP Core requests, failures, and performance.
category: Quality & Security
version: 1.0.0
status: active
---

# Observability

## Purpose
Help engineers diagnose operational behavior and detect degradation without exposing sensitive business data.

## Scope
Structured logs, request correlation, metrics, traces, health signals, and alerting requirements.

## When to Use This Skill
Use for new services/routes, high-risk workflows, performance work, and production failure handling.

## When NOT to Use This Skill
Do not call operational logs an immutable audit trail or add an observability vendor without a platform decision.

## SAP Core Context
No observability provider is specified. Use existing project/deployment capabilities when discovered; do not provision an external service speculatively. Business audit evidence belongs to audit-trail policy.

## Core Principles
Instrument user-impacting operations with useful low-cardinality signals, safe context, and explicit ownership.

## Mandatory Rules
- Correlate request and module/use-case logs with a non-secret request ID.
- Record outcome, duration, and safe error class for critical posting, stock, auth, and migration operations.
- Never log passwords, tokens, cookies, connection strings, full sensitive request bodies, or unnecessary PII.
- Keep high-cardinality values and sensitive IDs out of metric labels; use structured logs/traces with access controls.
- Define health/readiness semantics without exposing internals to unauthenticated users.

## Architecture Guidelines
Instrument boundaries: REST request, application use case, database dependency, file processing. Use consistent timestamps and severity, and record enough information to distinguish validation, authorization, conflict, and infrastructure failures. Separate operational logs/metrics from audit rows. Alert on user-impacting error rate/latency and queue/resource saturation only where such components exist.

## Implementation Guidelines
**Example:** A failed journal-post request logs request ID, module, company identifier only if approved for logs, operation name, safe error category, duration, and whether transaction committed. It does not log journal line payload, credentials, or full SQL parameters. Audit event remains a separate record.

## Data and Security Considerations
Restrict log access and retention; scrub exception context; treat traces and screenshots as potentially sensitive. Avoid exposing stack traces via health endpoints. Ensure operational telemetry cannot be used to enumerate HR/finance records.

## Common Anti-Patterns
PII in logs; high-cardinality tenant labels; logs as business proof; swallowed failures; health endpoint exposes DB URL; unbounded trace payload; alerts on expected validation errors.

## Validation Requirements
**Implementation checklist:** signal ownership; correlation; redaction; error taxonomy; latency/count measurement; retention/access; alert criteria. **Review checklist:** test failure log; verify no secrets; sample cardinality; correlation across route/use-case; audit remains separate; health response safe.

## Related Skills
error-handling, audit-trail, performance-optimization, release-management, application-security.

## Reference Documents
OpenTelemetry docs https://opentelemetry.io/docs/ and OWASP Logging Cheat Sheet https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html. No vendor/backend is selected; verify project support before adding instrumentation dependencies.
