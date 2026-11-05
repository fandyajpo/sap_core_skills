---
name: typescript-engineering
description: Establishes type-safe contracts and runtime boundary discipline for SAP Core TypeScript.
category: Architecture & Engineering
version: 1.0.0
status: active
---

# TypeScript Engineering

## Purpose
Use static types to make valid application states easier to express while preserving runtime validation at trust boundaries.

## Scope
Domain and API types, narrowing, module exports, compiler settings, and safe handling of unknown values.

## When to Use This Skill
Use for TypeScript code, shared contracts, DTOs, domain state models, or compiler configuration.

## When NOT to Use This Skill
Do not treat compile-time types as validation of HTTP bodies, files, database contents, or persisted JSON.

## SAP Core Context
TypeScript spans Next.js server and client code. Domain contracts should align with owning modules; Zod provides runtime validation for untrusted input.

## Core Principles
Make uncertainty explicit (`unknown` at boundaries), narrow before use, and avoid type assertions that conceal invalid data.

## Mandatory Rules
- Validate external/untrusted input at runtime with Zod or an equivalent approved schema.
- Do not expose server-only types/modules to client code if they carry secrets or database internals.
- Model document lifecycles with explicit states and transitions rather than unrelated booleans.
- Keep shared types at the owning contract boundary; do not create a universal `types.ts` dumping ground.

## Architecture Guidelines
Use distinct domain entities, transport DTOs, and persistence models where their semantics differ. Prefer discriminated unions for state-dependent data and exhaustive handling for business transitions. Avoid `any`; use it only with narrow justification and containment. Type database numeric and date semantics explicitly so currency, unit, and timezone meaning is not lost.

## Implementation Guidelines
**Example:** Parse request JSON as `unknown`, validate with the order-creation Zod schema, then map the validated command into the sales use case. Do not cast the body to `CreateOrderInput` and assume it is valid. Keep Prisma-generated types in server infrastructure; expose a minimal response DTO.

## Data and Security Considerations
Static types do not prevent mass assignment, over-posting, tenant leaks, or malformed serialized data. Build allowlisted input objects, not persistence objects spread from request data. Avoid leaking sensitive types through generated client declarations.

## Common Anti-Patterns
`as` to silence mismatches; `any` propagation; client-side trust of inferred type; one type used simultaneously as DB row, API response, and form model; booleans encoding impossible lifecycle combinations.

## Validation Requirements
**Implementation checklist:** strict/null-aware handling follows repository config; untrusted data validated; DTOs allowlist fields; unions exhaustive; server/client imports reviewed. **Review checklist:** no unsafe cast or `any` escape hatch without reason; negative validation cases exist; source of generated types is clear.

## Related Skills
input-validation, api-engineering, code-organization, react-engineering, prisma-orm.

## Reference Documents
Official TypeScript Handbook: https://www.typescriptlang.org/docs/handbook/ and https://www.typescriptlang.org/tsconfig. Confirm compiler behavior against the project's tsconfig and pinned TypeScript version.
