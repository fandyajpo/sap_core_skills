---
name: authorization-engineering
description: Applies CASL authorization policies to SAP Core server operations and company-scoped resources.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Authorization Engineering

## Purpose
Determine what an authenticated actor may do to a particular resource in a specific business context.

## Scope
CASL abilities, actions/subjects, conditions, resource ownership, server enforcement, and policy tests.

## When to Use This Skill
Use for every protected read, mutation, export, approval, posting, and organization-context change.

## When NOT to Use This Skill
Do not use UI visibility, authentication session validity, or a role string as the sole enforcement mechanism.

## SAP Core Context
CASL is the policy layer; Better Auth identifies the actor. Company/organization scope is part of authorization. All protected operations are checked on the server.

## Core Principles
Authorize the specific action on the specific resource and scope. Deny by default when policy is missing or context is ambiguous.

## Mandatory Rules
- Build abilities from trusted server-side actor/membership/role context, not client-provided roles or company IDs.
- Check permission at the application operation boundary and scope data access to the same company/resource conditions.
- Test conditions and negative cases; UI checks only improve usability.
- Separate authentication failure from authorization denial and avoid leaking resource existence.

## Architecture Guidelines
Define a controlled policy factory and stable action/subject vocabulary. Use CASL conditions for business/resource attributes only when the server query can enforce the same constraints; never fetch all records and filter after serialization. For write operations, authorize the intended transition and revalidate mutable fields/state within the transaction. Keep policy construction independent from UI presentation where possible; share safe policy metadata only if the server remains authoritative.

## Implementation Guidelines
**Example:** An ability may allow a user with the appropriate procurement permission to approve purchase orders only for their active company and within the permitted amount band. The server loads the order by both ID and company scope, checks ability and approval separation-of-duties, then performs a version/state-checked transition. A client-hidden button is not part of the security decision.

## Data and Security Considerations
Prevent IDOR, cross-company access, stale authorization, privilege escalation through self-editable role fields, and overbroad list/export queries. Rebuild or refresh policy when memberships/roles change; define session/cache invalidation expectations. Audit role grants and sensitive denials where required.

## Common Anti-Patterns
Frontend-only gates; `isAdmin` boolean for every domain; policy checks against unscoped objects; user-supplied tenant ID; wildcard access; trusting an ability built on stale client claims; checking ability before loading then mutating a changed row without state validation.

## Validation Requirements
**Implementation checklist:** actor source; company context; action/subject; conditions; query scope; transaction recheck; fail-closed behavior. **Review checklist:** allow and deny matrix; cross-company and guessed-ID tests; changed-state race; export/list filtering; UI and server behavior intentionally separated.

## Related Skills
authentication-engineering, rbac-authorization, api-engineering, core-organization, database-security, application-security.

## Reference Documents
CASL official guide https://casl.js.org/v6/en/guide/intro/ and docs https://casl.js.org/v6/en/. Verify APIs/condition matching against the installed CASL version; do not let client abilities replace server enforcement.
