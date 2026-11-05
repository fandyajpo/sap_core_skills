---
name: workflow-engine
description: Models explicit SAP Core document lifecycle transitions and reliable in-process workflow execution.
category: Enterprise Engineering
version: 1.0.0
status: active
---

# Workflow Engine

## Purpose
Make multi-step business lifecycles explicit, authorized, auditable, and recoverable.

## Scope
States, transitions, commands, guards, side effects, workflow configuration, and persistence.

## When to Use This Skill
Use for documents with multiple states, approvals, posting, receipt, fulfillment, or correction paths.

## When NOT to Use This Skill
Do not build a generic workflow engine for a small fixed lifecycle or implement distributed orchestration without need.

## SAP Core Context
The initial application is a modular monolith. Workflow transitions execute through owning modules and local transactions; no external broker is approved.

## Core Principles
A transition is a named business command with preconditions, authorization, effect, and audit—not an arbitrary status assignment.

## Mandatory Rules
- Define allowed transitions and terminal/reopen/cancel/reverse behavior.
- Validate current state and document version in the server-side transition.
- Keep transition rules in the owning module; configuration must not let an administrator bypass invariants.
- Make side effects idempotent and transaction-safe; record actor, time, and reason where applicable.

## Architecture Guidelines
Use a simple explicit transition model for bounded workflows. Store current state for efficient access and maintain history when audit/process reconstruction requires it. Version rules/configuration if older documents must retain behavior. Keep human approval as a separate authorization/decision concern. For long-running steps that cannot be one DB transaction, use durable state and retry/recovery; do not claim exactly-once execution.

## Implementation Guidelines
**Example:** A requisition command `submit` verifies nonempty lines, company scope, and draft version; it moves to Submitted and writes transition evidence atomically. `approve` is a different command requiring an authorized approver and separation-of-duties. A direct PATCH of `status: Approved` is not exposed.

## Data and Security Considerations
Every transition is subject to server authorization and org scope. Prevent replay using version/command identity. Do not expose internal workflow configuration or sensitive rejection reasons to unauthorized parties. Audit mutable rules and administrator changes.

## Common Anti-Patterns
Status string changed from UI; implicit state transitions in ORM hooks; one generic workflow table with no domain semantics; approvals bypassed on direct endpoint; side effects repeated on retry; unversioned config changing historical behavior.

## Validation Requirements
**Implementation checklist:** state graph; guard; permission; version/concurrency; transaction and side effects; idempotency; audit/recovery. **Review checklist:** legal/illegal transitions; stale command; duplicate retry; permission bypass; cancellation/reversal; migration for existing records.

## Related Skills
erp-domain-modeling, transaction-integrity, approval-management, concurrency-control, audit-trail.

## Reference Documents
SAP_CORE_CONTEXT.md; `erp-domain-modeling`, `transaction-integrity`, and `approval-management`. Workflow-specific legal states are domain/product decisions, not inferred from a generic framework.
