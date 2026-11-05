---
name: form-engineering
description: Builds reliable SAP Core forms with React Hook Form and Zod while retaining server-side authority.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Form Engineering

## Purpose
Capture user intent with clear validation, field lifecycle, accessible feedback, and safe submission.

## Scope
React Hook Form, Zod schemas, resolver integration, field arrays, server errors, and form submission.

## When to Use This Skill
Use for create/edit/post/approve forms and any user-provided structured data.

## When NOT to Use This Skill
Do not treat browser validation as the system of record or duplicate business authorization in the form.

## SAP Core Context
React Hook Form owns form state; Zod validates runtime values; the server validates again. Money, quantities, dates, company, and lifecycle fields have explicit semantics.

## Core Principles
Client feedback is an early usability check; the server revalidates current business state and permission before commit.

## Mandatory Rules
- Parse inputs as runtime values with Zod; do not trust TypeScript types or browser-provided hidden fields.
- Use the supported Zod resolver integration for the pinned library versions; verify input/output type handling.
- Provide labels, descriptions, field-level errors, form-level failure, keyboard access, and focus management.
- Map server field errors to the correct fields without exposing internal details.
- Prevent double submission and handle stale/conflicting server state explicitly.

## Architecture Guidelines
Keep form schema aligned with API contract but separate UI-specific coercion/defaults from domain command rules where semantics differ. Use controlled components only when required. For field arrays use stable client keys distinct from persisted row IDs. A display-formatted decimal must be parsed to an exact decimal string/representation, not binary float. On submit, send an allowlisted command DTO.

## Implementation Guidelines
**Example:** A purchase-order form validates positive line quantities and required vendor/plant fields locally. The server rechecks vendor/company eligibility, current approval limit, and inventory/pricing rules. If the order version changed, show a conflict/reload path instead of overwriting the newer document.

## Data and Security Considerations
Never place authorization in editable form fields. Avoid logging passwords, tokens, sensitive HR data, or full submitted bodies. Sanitize rich text and validate file inputs server-side. Client validation rules must not be more permissive than server rules.

## Common Anti-Patterns
Browser-only validation; `any` form values; spreading all values into database writes; silent numeric coercion; index as key in reorderable arrays; clearing server errors on every render; optimistic submit without rollback.

## Validation Requirements
**Implementation checklist:** Zod schema/resolver versions; accessible field/error wiring; numeric/date parsing; server error mapping; pending/duplicate state; allowlisted DTO. **Review checklist:** invalid payload direct API rejected; auth/company tampering rejected; keyboard flow; stale version/conflict; server errors safely shown.

## Related Skills
input-validation, react-engineering, api-engineering, date-time-engineering, application-state-management.

## Reference Documents
React Hook Form https://react-hook-form.com/get-started; Zod https://zod.dev/; resolver integration https://github.com/react-hook-form/resolvers. Check the installed versions and resolver's documented input/output types.
