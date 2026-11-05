---
name: human-resource-management
description: Establishes privacy-conscious employee, organization, attendance, and payroll foundations for SAP Core.
category: ERP Business Domain
version: 1.0.0
status: active
---

# Human Resource Management

## Purpose
Model workforce records with strict access boundaries, accurate lifecycle, and auditable changes.

## Scope
Employees, departments, positions, attendance, payroll foundations, and employee lifecycle.

## When to Use This Skill
Use for workforce identity, employment status, organizational assignment, time/attendance, or payroll-related data.

## When NOT to Use This Skill
Do not assume payroll compliance, jurisdictional labor law, or external payroll processing is solved by a technical schema.

## SAP Core Context
HR entities—including departments and positions listed in the initial schema—belong to `human_resources`; they may reference company/organization identifiers owned by `core`. Authentication users and employees are distinct concepts: one person may have no login or multiple relevant employment relationships according to policy.

## Core Principles
Collect the minimum necessary personal data. Limit access by purpose and role; treat payroll and attendance as sensitive records with explicit retention and audit.

## Mandatory Rules
- Define employee lifecycle and effective-dated department/position assignment.
- Do not assume an employee row is the Better Auth user or login account.
- Enforce server-side field- and record-level authorization for HR queries, exports, and mutations.
- Record changes to employment, attendance corrections, and payroll-related records with actor/time/reason.
- Establish jurisdiction, retention, consent/legal basis, and access policy before implementing sensitive fields or calculations.

## Architecture Guidelines
Keep HR-specific records and workflows in `human_resources`; reference core organization through contracts. Separate payroll inputs, calculation versions, approvals, and finalized outputs. Do not mutate finalized payroll evidence in place; correct through a traceable adjustment. Avoid broad HR payloads in shared user/profile DTOs. External payroll integrations are deferred.

## Implementation Guidelines
**Example:** A manager requests an employee's attendance correction. The server scopes the employee to the manager's authorized organization, checks CASL policy, records original and corrected value plus reason and actor, and routes approval if policy requires. A normal profile endpoint omits pay and sensitive identifiers.

## Data and Security Considerations
Classify PII and payroll fields; restrict logs, analytics, exports, and backups. Apply retention/deletion policies compatible with legal obligations. Protect against bulk enumeration and cross-company access; audit administrator access. Avoid storing secrets or unnecessary identity documents.

## Common Anti-Patterns
HR data embedded in general user table; all managers can see all employees; unrestricted spreadsheet export; silently overwritten attendance; unversioned payroll calculations; assuming compliance from encryption alone.

## Validation Requirements
**Implementation checklist:** data purpose/classification; owner; effective dates; record/field policy; audit and correction; retention; jurisdictional review. **Review checklist:** cross-org access denied; export constrained; sensitive values absent from logs; correction history preserved; employment and auth lifecycle not conflated.

## Related Skills
core-organization, authorization-engineering, database-security, audit-trail, input-validation.

## Reference Documents
SAP_CORE_CONTEXT.md; CASL docs https://casl.js.org/v6/en/; application-security. Jurisdiction-specific employment, privacy, attendance, and payroll rules require qualified domain/legal validation.
