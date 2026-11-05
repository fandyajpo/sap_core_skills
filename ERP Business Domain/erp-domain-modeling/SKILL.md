---
name: erp-domain-modeling
description: Models SAP Core ERP workflows as lifecycle-bearing business facts rather than generic CRUD.
category: ERP Business Domain
version: 1.0.0
status: active
---

# ERP Domain Modeling

## Purpose
Create business models that preserve operational, financial, organizational, and audit meaning across a document's full lifecycle.

## Scope
Aggregates, business documents, state transitions, invariants, ownership, snapshots, and domain collaboration.

## When to Use This Skill
Use when a feature changes an ERP process, introduces a business document, or crosses module boundaries.

## When NOT to Use This Skill
Do not treat UI forms or database tables as the business model, or invent a universal workflow engine for a simple lifecycle.

## SAP Core Context
SAP Core is a modular monolith over ten owned PostgreSQL schemas. Procurement, inventory, sales, finance, production, HR, master data, organization, and system capabilities remain domain-owned.

## Core Principles
Business documents have identity, ownership, allowed transitions, actor/time evidence, and consequences. Draft data may be editable; posted/confirmed facts require controlled correction.

## Mandatory Rules
- Define lifecycle states and legal transitions before CRUD endpoints.
- Identify transaction boundaries and concurrent/retry behavior for every state-changing command.
- Preserve historical snapshots when later master-data edits must not rewrite the original business agreement.
- Keep organization scope and authorization in every command/read path.
- Allocate a domain owner and schema before adding an entity.

## Architecture Guidelines
Model a use case in terms of intent (submit, approve, post, receive, reverse), not generic update. Aggregate boundaries should include invariants that must change atomically; avoid oversized aggregates that require unrelated locks. Cross-module workflows coordinate through explicit application contracts and document the failure/compensation policy. Do not add asynchronous/distributed complexity without a requirement.

## Implementation Guidelines
**Example:** A purchase order moves Draft → Submitted → Approved → PartiallyReceived/Received → Closed or Cancelled according to defined rules. Receipt records actual quantities and creates inventory movement atomically within the approved coordination boundary; it does not rewrite the ordered quantity. Invoice matching compares PO and receipt evidence. Exact states depend on the implemented process and must be explicitly agreed.

## Data and Security Considerations
Separate actor identity from business responsibility; record who performed a transition. Protect company-scoped records and sensitive HR/finance attributes. Preserve posted history; do not cascade-delete business facts.

## Common Anti-Patterns
CRUD-only status field; freely editing posted records; implicit transitions in UI; using one generic `status` with undocumented meanings; cross-module direct writes; losing order-time prices/terms; treating an approval as permission to post.

## Validation Requirements
**Implementation checklist:** actor, owner, states/transitions, invariants, snapshots, authorization, transaction/retry, audit, error/recovery defined. **Review checklist:** illegal transitions rejected server-side; duplicates/races handled; ledger consequences traceable; module boundaries and lifecycle tests present.

## Related Skills
modular-monolith, database-modeling, transaction-integrity, workflow-engine, approval-management.

## Reference Documents
SAP_CORE_CONTEXT.md; relevant module skills; PostgreSQL transactions https://www.postgresql.org/docs/current/tutorial-transactions.html.
