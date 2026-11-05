---
name: core-organization
description: Governs SAP Core company, organization, plant, fiscal-year, and user context data.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Core Organization

## Purpose
Represent the legal and operational structure that scopes ERP work and authorization.

## Scope
Companies, organizations, plants, fiscal years, organizational relationships, and association to users/roles.

## When to Use This Skill
Use when creating or changing company structure, operating units, fiscal periods, or actor context.

## When NOT to Use This Skill
Do not use org hierarchy as a replacement for module-owned business records or CASL policy.

## SAP Core Context
Initial organizational entities belong in `core`; users, roles, and permissions are listed there, but Better Auth manages authentication identity/session semantics. Company boundaries govern access to master data and transactions.

## Core Principles
Organization structure is explicit and historically meaningful. Membership/assignment grants a context, not automatically every permission within it.

## Mandatory Rules
- Distinguish legal company, operational organization, and plant/site; do not overload one identifier for all three.
- Define parent/child cardinality, effective dates, and allowed hierarchy changes.
- Associate every company-scoped document with a validated company context and relevant plant where required.
- Model fiscal years/periods by company or approved accounting scope; prevent posting to closed or out-of-range periods.

## Architecture Guidelines
Keep the hierarchy in `core`; master-data and transaction modules reference stable IDs through contracts and constraints. Do not assume the tree is the same as user access. User membership, role assignment, and CASL conditions express who may do what in which organization. Effective-dated changes must not silently change historical document meaning.

## Implementation Guidelines
**Example:** A user has an active company membership and selected company context. A purchase-order command uses that company from validated server context, confirms the plant belongs to it, checks the user's CASL ability for that resource, and persists both IDs. It does not trust `companyId` sent by the browser.

## Data and Security Considerations
Prevent cross-company IDOR at query and command boundaries. Do not reveal company existence to unauthorized actors. Audit membership/structure changes. Restrict deletion when referenced documents exist; use inactivation/effective dating instead of destructive hierarchy edits.

## Common Anti-Patterns
Company ID accepted as authority from request; role equal to organization membership; mutable hierarchy changing old report scope; fiscal year derived from wall-clock calendar year; plant/company relationship unchecked; duplicate organization trees per module.

## Validation Requirements
**Implementation checklist:** legal/operational meaning; parent relation/effective date; uniqueness; membership/role separation; fiscal-period rules; audit/retention. **Review checklist:** cross-company access test; plant-company consistency; period boundary and closed-period cases; history stable after org changes.

## Related Skills
master-data-management, authorization-engineering, rbac-authorization, database-modeling, financial-accounting.

## Reference Documents
SAP_CORE_CONTEXT.md; CASL docs https://casl.js.org/v6/en/; PostgreSQL constraints https://www.postgresql.org/docs/current/ddl-constraints.html. Define jurisdiction-specific fiscal calendar policy explicitly.
