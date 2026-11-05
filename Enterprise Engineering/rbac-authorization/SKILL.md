---
name: rbac-authorization
description: Models role and permission assignment as inputs to SAP Core CASL policies, with explicit organizational scope.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# RBAC Authorization

## Purpose
Govern role/permission assignment without confusing job titles, membership, and resource-level access.

## Scope
Roles, permission catalogs, assignments, organizational scope, delegation, revocation, and audit.

## When to Use This Skill
Use when defining roles, granting permissions, changing memberships, or managing role lifecycle.

## When NOT to Use This Skill
Do not model all policy as a static role name or assume RBAC alone covers ownership, status, amount, or company conditions.

## SAP Core Context
Initial users/roles/permissions are associated with `core`; CASL is the application policy evaluator. Better Auth remains responsible for authentication/session, not business roles as a substitute for authorization.

## Core Principles
Roles bundle capabilities for administration; final access is a policy decision over actor, action, resource attributes, and organization scope.

## Mandatory Rules
- Define a permission vocabulary with least privilege and explicit module ownership.
- Scope assignments to company/organization where the business rule requires it.
- Separate membership from role assignment and avoid self-grant paths.
- Enforce separation of duties for approval/posting/administration where required.
- Audit grant, revoke, expiry, and scope changes with actor and reason.

## Architecture Guidelines
A role can contribute CASL rules but should not be the entire ability. Use explicit assignment lifecycle and effective dates if business policy needs them. Prefer narrow permission names (for example, `procurement.order.approve`) over generic `write`. Role inheritance should be deliberate and cycle-free. Protect administrative endpoints with independent server policy; prevent administrators from granting themselves beyond their authority.

## Implementation Guidelines
**Example:** `ProcurementApprover` grants the ability to approve orders within an assigned company; the policy additionally checks order status and a limit attribute. A user who is a `FinanceClerk` in one company does not gain finance access in another. A high-value order may require a second approver with distinct identity.

## Data and Security Considerations
Resolve membership and assignments on the server. Revoke effect promptly when role changes; avoid stale cached abilities. Protect role-management audit logs. Do not expose permission catalog internals or sensitive org membership to unauthorized clients.

## Common Anti-Patterns
Job title used as permission; global role without scope; role names hardcoded across UI and backend independently; self-service elevation; implicit inheritance; no revocation audit; approval by same actor who prepared the transaction.

## Validation Requirements
**Implementation checklist:** permission catalog; assignment scope/lifecycle; grant authority; inheritance; separation-of-duties; policy mapping; audit. **Review checklist:** least privilege; cross-org; self-escalation; revocation latency; role changes audited; authorization tests for conditions and boundaries.

## Related Skills
authorization-engineering, core-organization, audit-trail, authentication-engineering, approval-management.

## Reference Documents
CASL https://casl.js.org/v6/en/; SAP_CORE_CONTEXT.md; `authorization-engineering`. Validate policy behavior with the pinned CASL release.
