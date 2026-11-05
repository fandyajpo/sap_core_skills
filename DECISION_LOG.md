# Architecture Decision Log

Decisions below establish the current baseline. A decision may be superseded only by adding a new decision that explicitly references it; do not silently edit history. Status `Accepted` means the baseline is selected, not that every version-specific implementation detail is already verified.

## ADR-001 — Start as a modular monolith

- **Context:** SAP Core starts from an empty foundation; domain boundaries and transaction integrity matter more than independent deployment.
- **Options considered:** Modular monolith; microservices; unstructured single application.
- **Selected approach:** One deployable modular monolith with domain-owned modules and explicit internal contracts.
- **Reasoning:** Preserves straightforward local transactions and operations while enforcing ownership; avoids distributed-system overhead without a demonstrated need.
- **Consequences:** Cross-module calls and table access require clear contracts; module boundaries must be reviewed as code evolves.
- **Status:** Accepted.

## ADR-002 — One PostgreSQL database with ten logical schemas

- **Context:** ERP domains share organization context and require reliable relational integrity and transactional operations.
- **Options considered:** Separate databases; one schema; one database with domain schemas.
- **Selected approach:** One database named `sap_core` with `core`, `master_data`, `procurement`, `inventory`, `sales`, `finance`, `production`, `human_resources`, `integration`, and `system` schemas.
- **Reasoning:** Supports centralized governance and domain-oriented ownership without distributed transactions. Prisma Postgres is the managed platform; PostgreSQL is the engine.
- **Consequences:** Schema/table ownership and cross-schema access must be controlled. Prisma multi-schema support and exact syntax must be verified against the selected Prisma version and provider.
- **Status:** Accepted.

## ADR-003 — Prisma ORM and Prisma Migrate are standard

- **Context:** The platform needs a consistent typed data-access and migration workflow.
- **Options considered:** Direct SQL-only access; alternate ORM; Prisma ORM with Prisma Migrate.
- **Selected approach:** Prisma Schema is the primary model declaration, Prisma ORM is the server-only access layer, and Prisma Migrate is the schema evolution mechanism. Raw SQL is an exception that requires justification and migration documentation.
- **Reasoning:** Establishes a common governed path for schema and application access while retaining PostgreSQL capabilities when explicitly needed.
- **Consequences:** Version-specific features, provider compatibility, and multi-schema support must be checked before relying on them. Applied migration files are immutable history.
- **Status:** Accepted.

## ADR-004 — Separate authentication from authorization

- **Context:** Identity/session validity does not determine access to company-scoped ERP resources.
- **Options considered:** Authentication library roles as the sole policy; application policy layer.
- **Selected approach:** Better Auth handles identity and sessions; CASL models application capabilities and resource conditions; protected server operations enforce the policy.
- **Reasoning:** Keeps session lifecycle distinct from business authorization and supports organization/resource conditions.
- **Consequences:** Policies require centralized modeling and tests. Frontend permission checks are presentation only.
- **Status:** Accepted.

## ADR-005 — Defer external integrations

- **Context:** Initial priority is the application and its own database; third-party systems add operational, security, and consistency complexity.
- **Options considered:** Build a generalized integration engine early; reserve architectural space and defer implementation.
- **Selected approach:** Defer external integrations, brokers, and synchronization. Keep the `integration` schema reserved without implementing an integration engine.
- **Reasoning:** Avoids speculative complexity while leaving future decisions possible.
- **Consequences:** Current workflows must be self-contained. Future integrations need a new decision and explicit consistency/security model.
- **Status:** Accepted.

## ADR-006 — Server/client state responsibilities

- **Context:** The approved frontend stack includes multiple state libraries with overlapping capabilities.
- **Options considered:** One global store; separate state ownership by lifecycle.
- **Selected approach:** TanStack Query for server state, Zustand for shared client/UI state, React state for isolated local state, React Hook Form for form state, URL parameters for shareable navigation state.
- **Reasoning:** Keeps cache invalidation and data authority distinct from transient UI state and forms.
- **Consequences:** Avoid duplicated server data in client stores; define ownership per feature.
- **Status:** Accepted.

## ADR-007 — Vite+ is not presumed to replace Next.js

- **Context:** Both technologies appear in the approved stack, but responsibilities and compatibility are not established by naming alone.
- **Options considered:** Treat Vite+ as the application runtime/build system; evaluate it only for compatible development tasks.
- **Selected approach:** Next.js remains the application framework and owns its supported routing/rendering/build workflow. Vite+ may own only tasks proven compatible for the pinned versions.
- **Reasoning:** Avoids unsupported assumptions about replacing framework-specific compilation, routing, or production output.
- **Consequences:** A concrete Vite+ usage requires compatibility evidence and a recorded decision before it replaces native tooling.
- **Status:** Accepted; exact Vite+ task compatibility requires verification.

## ADR-008 — Preserve ERP history through immutable posted facts

- **Context:** Financial and inventory history must remain auditable and reconcilable.
- **Options considered:** Mutate posted records; append reversals/adjustments and traceable movements.
- **Selected approach:** Posted journal entries are immutable and corrected by reversal/adjustment. Inventory changes are traceable stock movements with transactionally maintained balances.
- **Reasoning:** Preserves audit evidence and allows reconciliation.
- **Consequences:** Posting and inventory operations need explicit transactions, idempotency/retry behavior, and concurrency controls.
- **Status:** Accepted.
