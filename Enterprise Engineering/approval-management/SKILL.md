---
name: approval-management
description: Implements attributable, policy-bound SAP Core approval decisions and separation of duties.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Approval Management

## Purpose
Ensure review decisions are made by authorized actors against the exact document version and policy that requires approval.

## Scope
Approval rules, assignees, thresholds, decisions, delegation, reapproval, and audit.

## When to Use This Skill
Use for procurement, financial, master-data, or other business actions requiring review before execution.

## When NOT to Use This Skill
Do not treat an approval record as automatic authorization to post or as a replacement for the owning module's invariants.

## SAP Core Context
Approval policy is evaluated through CASL and domain rules; workflow transitions remain module-owned. Approval data belongs with its owning workflow unless a justified shared system capability is approved.

## Core Principles
Approval is a recorded decision over a versioned business proposal, by a qualified actor, under a known policy.

## Mandatory Rules
- Bind decisions to document ID, version/content fingerprint as appropriate, policy version, actor, time, and outcome.
- Enforce separation of duties where the policy requires it (for example, requester cannot approve own high-risk request).
- Re-evaluate approval if material fields change after approval.
- Enforce decision permission on the server and record delegation/override reasons.
- Keep approval and final posting/fulfillment as distinct transitions.

## Architecture Guidelines
Define threshold and route rules explicitly; avoid hardcoded thresholds duplicated across modules. Approval changes are audited. Rejection, withdrawal, expiry, escalation, and re-submission semantics are explicit. Use a simple in-process workflow first. Never let mutable policy retroactively imply that an old document was approved under a new rule without deliberate version semantics.

## Implementation Guidelines
**Example:** A purchase order at a given revision enters PendingApproval. The approver checks the exact revision; if price/quantity changes, the system invalidates the decision and requests reapproval. An approver different from the requester records approve/reject with reason; a separate command later posts the order after rechecking state and access.

## Data and Security Considerations
Do not leak approval queues across companies. Protect sensitive approval comments. Audit changes to approver assignments and policy. Enforce least privilege for delegates and administrators.

## Common Anti-Patterns
Approval by current status only; mutable document after approval; self-approval; UI-only thresholds; same capability grants both request and approve; approval automatically posts; silent admin override.

## Validation Requirements
**Implementation checklist:** policy/version; document revision; approver resolution; CASL; separation-of-duties; material-change/reapproval rule; decisions/audit; override path. **Review checklist:** unauthorized/self approval denied; changed revision invalidates decision; concurrent decision resolved; cross-company queue isolated; final action rechecks authority/state.

## Related Skills
workflow-engine, authorization-engineering, rbac-authorization, audit-trail, procurement-management, transaction-integrity.

## Reference Documents
`workflow-engine`, `authorization-engineering`, and `audit-trail`; CASL official docs https://casl.js.org/v6/en/. Thresholds and separation-of-duties policy require explicit product approval.
