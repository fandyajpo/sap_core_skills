---
name: api-engineering
description: Defines reliable, secured internal REST contracts for SAP Core modules.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# API Engineering

## Purpose
Expose internal application capabilities through stable, validated, resource-oriented REST endpoints.

## Scope
Routes, HTTP semantics, DTOs, errors, pagination/filtering, idempotency, and API evolution.

## When to Use This Skill
Use for every endpoint addition/change and for consumers of internal REST resources.

## When NOT to Use This Skill
Do not create external integration gateways or transport-facing business logic that bypasses module use cases.

## SAP Core Context
REST is the internal API. Next.js may host it according to the pinned application architecture; the route is an adapter, not the business-rule owner. Third-party integrations are deferred.

## Core Principles
A contract defines accepted input, response shape, access policy, side effects, and failure behavior. HTTP success is not proof that business invariants were met.

## Mandatory Rules
- Authenticate, authorize with CASL, and enforce company/resource scope server-side on every protected operation.
- Parse and validate all inputs; bound pagination, sort keys, filters, and payload size.
- Use HTTP methods/status codes consistently with the operation and return a stable safe error envelope.
- Make retry-sensitive commands idempotent where duplicate execution could create duplicate business effects.

## Architecture Guidelines
Use resource-oriented paths and explicit DTOs; avoid leaking Prisma models. List endpoints use server-side pagination and allowlisted sorting/filtering. Keep endpoint versioning compatible by default; introduce a version only for an intentional incompatible contract and document its support/deprecation policy. Commands that post or reserve resources should make lifecycle and concurrency semantics explicit.

## Implementation Guidelines
**Example:** `POST` a validated sales order command with an idempotency token; the server resolves actor/company, authorizes customer and plant scope, invokes the sales use case, and returns a document DTO plus appropriate status. A retry resolves the same outcome rather than creating a second order. Do not assume idempotency headers are globally standardized; define the app contract.

## Data and Security Considerations
Prevent IDOR by scoped queries, not by checking a caller-supplied company ID alone. Avoid SQL detail in errors. Use safe allowlists for sort/filter fields and parameterized queries. Ensure cache policy cannot share protected responses across actors.

## Common Anti-Patterns
Returning raw ORM rows; unbounded list routes; trusting client-supplied role/company; HTTP 200 for every outcome; inconsistent errors; GET endpoints with side effects; relying on UI validation; silent retries of non-idempotent writes.

## Validation Requirements
**Implementation checklist:** request and response schemas; actor, policy, scope; bounded query controls; documented status/error; transaction/retry behavior. **Review checklist:** unauthenticated/forbidden/cross-company/invalid/duplicate cases; no data overexposure; contract tests and pagination boundaries verified.

## Related Skills
nextjs-engineering, input-validation, authentication-engineering, authorization-engineering, error-handling, tanstack-query.

## Reference Documents
Next.js docs https://nextjs.org/docs and HTTP semantics in RFC 9110 https://www.rfc-editor.org/rfc/rfc9110. Check the framework's current route handler contract for the pinned version.
