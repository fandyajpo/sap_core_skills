---
name: date-time-engineering
description: Preserves business date, instant, timezone, and locale semantics throughout SAP Core.
category: Frontend & Experience Engineering
version: 1.0.0
status: active
---

# Date-Time Engineering

## Purpose
Prevent off-by-one dates, ambiguous timestamps, and incorrect fiscal-period behavior.

## Scope
Date-only values, instants, local business times, parsing, display, filtering, and date-fns usage.

## When to Use This Skill
Use for persisted dates/times, filters, scheduling, payroll attendance, fiscal periods, and reports.

## When NOT to Use This Skill
Do not treat browser locale or server local timezone as the business timezone.

## SAP Core Context
PostgreSQL distinguishes timestamp with and without time zone. date-fns is approved for date utilities; no timezone extension library is assumed approved.

## Core Principles
Choose the semantic type first: calendar date, instant, or local wall-clock time in a named business timezone.

## Mandatory Rules
- Use a date-only representation for values such as fiscal date of record when no instant exists; do not convert it through UTC and shift the calendar day.
- Store instants with explicit timezone-aware semantics and serialize in a documented canonical form.
- Require an explicit company/business timezone for local-time interpretation and daylight-saving boundaries.
- Parse input strictly at the boundary; do not rely on permissive JavaScript `Date` parsing of ambiguous strings.
- Do not use date-fns formatting as a substitute for timezone conversion; use only documented supported semantics.

## Architecture Guidelines
Keep DB type/API representation/UI display mapping explicit. Fiscal periods belong to company/accounting policy, not system calendar assumptions. Represent all-day ranges as half-open intervals where possible (`start <= instant < next day`) in the chosen timezone. If richer IANA timezone conversion is needed beyond native platform capabilities, make a documented dependency decision rather than silently adding a package.

## Implementation Guidelines
**Example:** A posting date is a date-only ISO calendar value checked against the company's open fiscal period. An audit event timestamp is an instant. Display converts the instant to the user's/organization's declared timezone, while filtering uses the same declared scope and boundary rules.

## Data and Security Considerations
Timezones can affect authorization, payroll, period cutoff, and audit interpretation. Preserve original zone/context for appointments or shifts where necessary. Do not trust client-supplied timezone to change accounting period authority.

## Common Anti-Patterns
Date-only stored as UTC midnight; ambiguous local timestamp; timestamp without zone for an instant; comparing date strings lexically without format guarantees; system timezone in reports; silently parsing invalid dates.

## Validation Requirements
**Implementation checklist:** semantic classification; serialization contract; DB type; timezone source; DST policy; fiscal boundary; parse/error behavior. **Review checklist:** UTC-offset and DST boundary tests; leap day; company/user zone difference; date-only round-trip; no accidental day shift.

## Related Skills
postgresql-design, input-validation, financial-accounting, human-resource-management, tanstack-table.

## Reference Documents
PostgreSQL date/time docs https://www.postgresql.org/docs/current/datatype-datetime.html; date-fns docs https://date-fns.org/docs/Getting-Started. Confirm timezone capabilities against selected libraries; no date-fns timezone add-on is implied.
