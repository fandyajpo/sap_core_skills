# Global Engineering Rules

These rules apply to every SAP Core change and every skill. A domain-specific skill may strengthen a rule but may not weaken it without a recorded architecture decision.

## Architecture and ownership

1. Keep the initial product a single deployable modular monolith. Do not introduce microservices, distributed workflows, or external integration infrastructure without a demonstrated requirement and approved decision.
2. Every business module owns its entities, invariants, application services, and write paths. Cross-module reads/writes must use documented contracts; no convenience import or arbitrary access to another module's tables.
3. Keep domain rules out of UI components, route handlers, and ORM hooks. Route handlers adapt HTTP to application use cases; domain/application services enforce business behavior.
4. Shared code must be cohesive, narrowly scoped, and business-neutral unless it is explicitly a shared domain concept.
5. Establish entity ownership, lifecycle, constraints, authorization, audit rules, and transaction boundary before implementation.

## Data and database

6. PostgreSQL is the engine, Prisma Postgres is the platform, Prisma ORM is the data-access layer, and Prisma Migrate is the migration system. Never conflate them.
7. Use one `sap_core` database and the ten governed schemas in `SAP_CORE_CONTEXT.md`. Do not create another database/schema casually.
8. Prisma Client and database credentials stay server-side. Never serialize ORM clients, credentials, internal errors, or unrestricted records to the browser.
9. Use foreign keys and database constraints for invariants that must survive concurrent requests and alternate application paths. Application validation complements, never replaces, database integrity.
10. Use exact decimal/numeric representations for monetary and precision-sensitive quantities; never use binary floating-point for financial arithmetic. Specify currency, scale, rounding, and unit semantics.
11. Use transactions when multiple writes form one business fact. Keep transactions bounded; do not perform network calls or long CPU work inside them.
12. Treat applied migrations as immutable history. Add a forward migration for change; assess rollout/backfill/rollback safety and production data compatibility.
13. Make time semantics explicit: distinguish an instant from a date-only business value, store/compare instants with timezone-aware semantics, and do not infer a company's business date from a user's browser locale.
14. Prefer normalized relational data for business facts. JSON is not a shortcut around schema ownership, validation, indexing, or relationships.
15. Use stable internal IDs separately from human-readable document numbers. Allocate numbers atomically and with explicit scope/gap policy.

## Security and access

16. Authentication answers who the actor is; authorization answers whether that actor may perform this action on this resource in this organization. Enforce both on every server-side protected operation.
17. Better Auth owns authentication/session mechanisms; CASL expresses authorization policy. Do not treat a role label or a hidden button as authorization.
18. Scope database reads and writes by the authenticated organization/company context and validate resource ownership to prevent IDOR and cross-tenant exposure.
19. Validate all untrusted input at server boundaries with runtime schemas. TypeScript types are erased and do not validate requests, files, database state, or third-party content.
20. Avoid logging passwords, cookies, tokens, secrets, payment/identity data, or full sensitive payloads. Return safe errors without SQL, stack traces, or internal identifiers.
21. Treat uploaded files, spreadsheet cells, and rich text as hostile. Enforce size, type, parsing, sanitization, resource and output-encoding controls.

## Application and UI

22. Use TanStack Query for remote state, Zustand for shared client/UI state, React local state for isolated component state, React Hook Form for form state, and URL parameters for shareable navigation/filter state. Avoid duplicate server caches.
23. Use Zod for runtime boundary validation and React Hook Form for form lifecycle. Validate again on the server.
24. For ERP-scale tables, paginate/filter/sort on the server, use stable row IDs, and fetch only the requested slice. Virtualization reduces rendered DOM; it does not make fetching an unbounded dataset safe.
25. Tailwind CSS is the primary styling system. Motion is reserved for meaningful, accessible transitions and must respect reduced-motion preferences.
26. Keep application business logic out of Storybook stories. Use stories to document real component states and interactions.
27. Do not assume Vite+ replaces Next.js. Establish a documented, version-specific compatible task before delegating any build or routing responsibility to it.

## Business integrity

28. Model ERP documents as lifecycle-bearing aggregates with legal transitions, actor/organization scope, and explicit draft/posted/cancelled/reversed semantics.
29. Posted finance records are immutable; correct through reversals/adjustments and preserve links to originals.
30. Stock movements are an auditable ledger; update stock projections atomically and reconcile them. Define negative-stock policy and locking/concurrency behavior explicitly.
31. Approval decisions are server-authorized, attributable, time-stamped, and bound to the document version/state reviewed.
32. Audit records are append-oriented evidence; do not claim tamper-proofing unless a concrete control provides it.

## Verification and delivery

33. Follow `DEVELOPMENT_WORKFLOW.md`: inspect relevant skills, dependencies, module ownership, failure paths, and data effects before coding.
34. Run only checks actually available and relevant. Report commands/checks not run and unresolved version assumptions.
35. Review migration safety, authorization, tenant/company scoping, concurrency, error behavior, accessibility, and sensitive-data handling in addition to happy paths.
36. When a change affects an architectural decision, update `DECISION_LOG.md`, `SAP_CORE_CONTEXT.md` if applicable, the dependency map, index, and affected skills together.
