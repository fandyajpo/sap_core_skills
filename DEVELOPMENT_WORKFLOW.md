# AI-Assisted Development Workflow

Use this workflow for every SAP Core change, including code generation and review. Do not begin implementation merely because a file or endpoint seems obvious.

1. **Understand the request.** Identify expected behavior, actor, business outcome, acceptance criteria, constraints, failure cases, and whether the work changes data or external behavior. Ask only for missing decisions that materially alter the design.
2. **Identify affected modules.** Map UI, REST boundary, application use case, domain owner, database schema/tables, authorization policy, audit requirements, and reporting/read paths. Check for cross-module writes.
3. **Read relevant skills.** Start with `GLOBAL_RULES.md` and `SAP_CORE_CONTEXT.md`; use `SKILL_INDEX.md` and `SKILL_DEPENDENCY_MAP.md` to read all direct dependencies and relevant references/checklists.
4. **Analyze dependencies and current implementation.** Inspect real project files, pinned package versions, existing patterns, migrations, callers, constraints, and tests. Do not infer APIs or existing schema behavior from a skill alone.
5. **Propose the implementation.** State the smallest coherent design: contract, lifecycle, ownership, authorization, data changes, transaction boundary, error semantics, migration/deployment sequence, and tests. Record a new architecture decision if the baseline changes.
6. **Validate architecture before editing.** Confirm modular-monolith boundaries, schema ownership, no deferred integration, no client exposure of server-only data access, and no hidden business rule in UI/route code.
7. **Implement.** Make scoped changes following established conventions. Use server-side Zod validation, explicit authorization, safe database constraints, and atomic transactions where needed. Preserve applied migration history.
8. **Run relevant checks.** Run the repository's actual formatting, type, lint, unit/integration, migration, and browser checks when available. Do not invent commands. If this environment cannot execute a check, say so and identify the unverified risk.
9. **Review security and data integrity.** Examine authorization bypasses, organization isolation, IDOR, replay/idempotency, concurrency/race behavior, rollback/failure behavior, sensitive logging, file attack surface, exact numeric/time semantics, and audit evidence.
10. **Report results.** Summarize behavior changed, files/areas affected, tests actually run and results, migration/deployment considerations, and known limitations or assumptions. Never claim an unrun test passed.

## Change-specific review prompts

- **Schema/migration:** Can old and new application versions coexist during rollout? Is backfill bounded/restartable? Are uniqueness and check constraints safe to add? Can the data be reconstructed or reconciled?
- **Financial posting:** Are totals balanced at the posting boundary using exact decimals? Is a retry idempotent? Is a posted entry immutable? Does reversal preserve the source relationship?
- **Inventory:** Are movement, balance, reservation, and document state changes atomic? What happens under concurrent writers, retries, and insufficient stock?
- **Authentication/authorization:** Is the session validated server-side? Is the subject/resource/company condition checked at the operation boundary? Are denial and stale-session cases tested?
- **UI/API:** Are request/response contracts runtime validated? Are pagination/filter/sort inputs bounded and allowlisted? Does the UI avoid treating client cache or visibility as authority?
- **Files and exports:** Are untrusted bytes and cell values bounded, validated, sanitized, and encoded for the output context? Is memory usage bounded?
