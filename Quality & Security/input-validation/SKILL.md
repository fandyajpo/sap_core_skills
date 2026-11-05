---
name: input-validation
description: Validates SAP Core requests and data with Zod at every untrusted runtime boundary.
category: Quality & Security
version: 1.0.0
status: active
---

# Input Validation

## Purpose
Reject malformed or unauthorized-shaped data before it can enter domain workflows or persistence.

## Scope
REST bodies/params/query strings, forms, files, imports, dates, decimals, rich text, and validation errors.

## When to Use This Skill
Use for every untrusted request, file, stored external-like payload, and user-editable business field.

## When NOT to Use This Skill
Do not rely on TypeScript types or client-side form validation as server validation.

## SAP Core Context
Zod is the approved runtime validation library; React Hook Form may use a resolver on the client, but server validation remains mandatory. External integrations are deferred.

## Core Principles
Parse unknown input into an explicit command; distinguish syntactic validity from business permission and current-state rules.

## Mandatory Rules
- Treat request bodies as `unknown` until parsed by a runtime schema.
- Allowlist fields; reject or intentionally strip unknown keys according to endpoint compatibility policy.
- Bound strings, arrays, numeric ranges, pagination, nesting, and file sizes to prevent abuse.
- Validate decimals/dates without lossy coercion; define currency, units, timezone, and null/empty semantics.
- Revalidate persisted/current domain state and authorization after schema parsing.

## Architecture Guidelines
Keep schemas near the owning contract; reuse shared primitives only when semantic rules are identical. Separate transport schema from domain invariants where dependencies differ. Avoid permissive coercion that turns malformed values into valid-looking values. Return safe field errors without echoing secrets or full submitted content. Validate uploads by bytes/content and format, not extension alone.

## Implementation Guidelines
**Example:** Parse a sales-order request with an allowlisted Zod schema: customer ID, plant ID, currency, line IDs/quantities and optional idempotency key. The use case then resolves current customer/company access, UOM conversion, pricing and stock rules. A `quantity: "1e999"` or unknown `isApproved` field is rejected rather than blindly mapped into persistence.

## Data and Security Considerations
Validation is not sanitization for HTML/SQL and not authorization. Use parameterized queries and context-appropriate output encoding. Avoid logging invalid secret values. Prevent regex/recursive schema denial-of-service with bounded inputs.

## Common Anti-Patterns
Client-only checks; `as` cast of JSON; schema that accepts arbitrary key/value objects; unknown field mass assignment; float coercion for money; trusting MIME/extension; same schema assumed to encode all business policy.

## Validation Requirements
**Implementation checklist:** unknown input; allowlist; limits; exact conversions; date/file rules; safe errors; downstream authorization/state check. **Review checklist:** malformed/omitted/null/extra fields; boundary lengths/ranges; precision/timezone; invalid encoding; direct API bypass; no secret echo.

## Related Skills
typescript-engineering, form-engineering, api-engineering, application-security, xlsx-processing, sharp-image-processing.

## Reference Documents
Zod official docs https://zod.dev/; OWASP Input Validation Cheat Sheet https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html. Verify Zod APIs against the pinned version.
