# Technical Reference Register

Official documentation is the primary source for technical guidance. A “checked” entry means the cited documentation content was actually reviewed; it does not mean that SAP Core's unpinned dependency combination was built, tested, or found compatible. The SAP Core skills repository has no application lockfile or configured database. Version-specific claims below are bounded to the documentation named and date checked.

## Database, ORM, and authentication references

| Technology / page | Official reference | Status and scope checked | Checked date |
|---|---|---|---|
| Prisma Postgres | https://www.prisma.io/docs/postgres | Official platform documentation reviewed. Current page describes managed PostgreSQL 17, PgBouncer pooling, pooled app connection, direct migration connection, and provider-specific operational features. No service was provisioned and no plan/configuration was selected. | 2026-11-05 |
| Prisma ORM multi-schema | https://www.prisma.io/docs/orm/v7/prisma-schema/data-model/multi-schema | Official v7 guide reviewed: PostgreSQL support, datasource `schemas`, model/enum `@@schema`, schema-aware migrations/queries, and cross-schema relations. Official Prisma ORM 6.13 release material reports multi-schema GA. This does not verify ORM 8 or the future app schema. | 2026-11-05 |
| Prisma ORM / Prisma Migrate | https://www.prisma.io/docs/orm/migrations/applying-a-migration | Official current migration page reviewed. ORM 7's `migrate deploy` and current ORM 8 release-candidate planning/`db migrate` workflows differ. Do not apply either command sequence until the application CLI version is pinned. | 2026-11-05 |
| Prisma ORM transactions | https://www.prisma.io/docs/orm/fundamentals/transactions | Official current page reviewed. ORM 8 RC uses `db.transaction`, differs from ORM 7 `$transaction`, documents no `isolationLevel`/timeout/maxWait option, and requires explicit retry handling for write conflicts. No SAP Core API signature is prescribed. | 2026-11-05 |
| Prisma ORM multi-schema release status | https://github.com/prisma/web/blob/main/apps/blog/content/blog/orm-6-13-0-ci-cd-workflows-and-pgvector-for-prisma-postgres/index.mdx | Official Prisma release material reviewed; it reports multi-schema as generally available in Prisma ORM 6.13.0. Version/provider compatibility remains to be checked against the eventual application lockfile. | 2026-11-05 |
| PostgreSQL 17 constraints | https://www.postgresql.org/docs/17/ddl-constraints.html | Official PostgreSQL 17 page reviewed for `CHECK` true-or-null behavior, prohibition on cross-row `CHECK` guarantees, unique NULL behavior, FK semantics, and no automatic index on referencing columns. | 2026-11-05 |
| PostgreSQL numeric types | https://www.postgresql.org/docs/17/datatype-numeric.html | Official PostgreSQL 17 page reviewed for exact `numeric`, precision/scale and storage rounding semantics, and distinction from inexact floating point. Prisma/client mapping and SAP Core scale decisions remain untested. | 2026-11-05 |
| PostgreSQL date/time | https://www.postgresql.org/docs/17/datatype-datetime.html | Official PostgreSQL 17 page reviewed for date/timestamp/time-zone semantics. Time-zone-aware timestamps are stored as UTC instants; the original named timezone is not retained. | 2026-11-05 |
| PostgreSQL isolation | https://www.postgresql.org/docs/17/transaction-iso.html | Official PostgreSQL 17 page reviewed for default Read Committed behavior, serialization anomalies, and retry requirements at stricter levels. | 2026-11-05 |
| PostgreSQL explicit locking | https://www.postgresql.org/docs/17/explicit-locking.html | Official PostgreSQL 17 page reviewed for row-level locking, conflicting writers/lockers, deadlocks, lock ordering, and transaction-duration concerns. | 2026-11-05 |
| PostgreSQL sequences | https://www.postgresql.org/docs/17/functions-sequence.html | Official PostgreSQL 17 page reviewed; concurrent sequence values are distinct, and consumed values are not reclaimed on rollback, so gapless numbering cannot be promised by ordinary sequences. | 2026-11-05 |
| Better Auth Prisma adapter | https://www.better-auth.com/docs/adapters/prisma | Official guide reviewed. Current guide is written for Prisma 7 and distinguishes Prisma 6 and earlier. Exact generator/model mapping and chosen adapter setup still require version-pinned validation. | 2026-11-05 |
| Better Auth adapter peer dependencies | https://github.com/better-auth/better-auth/blob/main/packages/prisma-adapter/package.json | Current official repository package metadata reviewed through Context7; peer range lists Prisma 5, 6, and 7, not Prisma 8. Treat as the package state observed on the review date, not a promise about later releases. | 2026-11-05 |
| Better Auth session management | https://www.better-auth.com/docs/concepts/session-management | Official page reviewed for server-verified cookie sessions, configurable session lifetime/freshness, optional cookie cache, and revocation freshness implications. Library defaults are not SAP Core policy. | 2026-11-05 |
| Better Auth cookies | https://www.better-auth.com/docs/concepts/cookies | Official page reviewed for cookie behavior, secure production defaults, and cross-subdomain scope cautions. Deployment behavior remains unconfigured. | 2026-11-05 |
| Better Auth email/password | https://www.better-auth.com/docs/authentication/email-password | Official page reviewed for verification/reset delivery callbacks and email-enumeration considerations. Email delivery remains deferred. | 2026-11-05 |
| Better Auth Next.js integration | https://www.better-auth.com/docs/integrations/next | Official integration page previously consulted; exact handler/cookie behavior is version- and Next.js-version-sensitive. No app integration was tested. | 2026-11-05 |
| CASL v6 guide | https://casl.js.org/v6/en/guide/intro/ | Hosted guide did not return readable text through the web reader. The official project repository documentation was retrieved through Context7; general condition checks and `rulesToQuery` behavior were reviewed. Exact installed API and any Prisma-specific translator are not verified. | 2026-11-05 (hosted page unreadable) |
| CASL ability-to-query documentation | https://github.com/stalniy/casl/blob/main/docs-src/src/content/pages/advanced/ability-to-database-query/en.md | Official project repository documentation retrieved through Context7; describes converting CASL conditions to database query predicates. No adapter-specific SAP Core implementation was tested. | 2026-11-05 |

## Other approved-stack reference status

The following are official reference pointers already present in the skills but were not independently content-verified during this database-prioritized second pass. Package versions and runtime integrations remain unselected. A pointer is not evidence of API compatibility.

| Technology | Official reference pointer | Status |
|---|---|---|
| Next.js | https://nextjs.org/docs | Not independently content-checked in this pass; pinned framework APIs/caching remain unverified. |
| React | https://react.dev/learn | Reference pointer only; version unpinned. |
| TypeScript | https://www.typescriptlang.org/docs/ | Reference pointer only; version/config unpinned. |
| Tailwind CSS | https://tailwindcss.com/docs | Reference pointer only; v3/v4 setup remains version-sensitive. |
| Motion | https://motion.dev/docs | Reference pointer only; import/API behavior version-sensitive. |
| Vite+ | https://viteplus.dev/ | Reference pointer only; compatibility with the Next.js application workflow is not established. |
| Zod | https://zod.dev/ | Reference pointer only; version unpinned. |
| React Hook Form / resolvers | https://react-hook-form.com/get-started and https://github.com/react-hook-form/resolvers | Reference pointers only; resolver input/output typing is version-sensitive. |
| TanStack Query / Table / Virtual | https://tanstack.com/query/latest/docs/framework/react/overview, https://tanstack.com/table/latest/docs/introduction, https://tanstack.com/virtual/latest/docs/introduction | Moving `latest` pointers only; versions and option/default behavior unverified. |
| Zustand | https://zustand.docs.pmnd.rs/ | Reference pointer only; version/middleware unpinned. |
| date-fns | https://date-fns.org/docs/Getting-Started | Reference pointer only; timezone conversion capability is not implied. |
| Apache ECharts | https://echarts.apache.org/en/option.html | Reference pointer only; wrapper/lifecycle integration unverified. |
| Tiptap | https://tiptap.dev/docs | Reference pointer only; extensions/schema compatibility unverified. |
| React PDF renderer | https://react-pdf.org/ | Reference pointer only; package identity/runtime choice remains unresolved. |
| Lucide React | https://lucide.dev/guide/packages/lucide-react | Reference pointer only; version unpinned. |
| Sharp | https://sharp.pixelplumbing.com/ | Reference pointer only; native binaries and deployment runtime unverified. |
| XLSX / SheetJS | https://docs.sheetjs.com/ | Reference pointer only; package/edition and API surface remain unresolved. |
| Playwright | https://playwright.dev/docs/intro | Reference pointer only; package/browser versions and test setup unverified. |
| Storybook | https://storybook.js.org/docs | Reference pointer only; Next.js and interaction-test integration unverified. |
| OWASP ASVS / Cheat Sheets | https://owasp.org/www-project-application-security-verification-standard/ and https://cheatsheetseries.owasp.org/ | Standards pointers only; no compliance assessment or control-by-control mapping performed. |
| HTTP RFC 9110 / RFC 9457 | https://www.rfc-editor.org/rfc/rfc9110 and https://www.rfc-editor.org/rfc/rfc9457 | Standards pointers only; response-envelope implementation not reviewed. |
| OpenTelemetry | https://opentelemetry.io/docs/ | Reference pointer only; no telemetry package/backend selected. |

## Verification record

- **Documentation review date:** 2026-11-05 UTC.
- **Compatibility status:** No SAP Core package or provider pair was built, installed, configured, or tested. PostgreSQL 17 is the engine version described by the current Prisma Postgres page, not an observed provisioned database.
- **Version-sensitive notices:** Prisma ORM 8 material cited here is release-candidate documentation at the time checked. Prisma 7 and earlier instructions must not be transplanted into ORM 8, or vice versa, without rechecking official docs.
- **CASL limitation:** Hosted v6 guide unreadable to the page reader; official repository documentation was retrieved through Context7. No CASL/Prisma query adapter or installed package was validated.
- **How to update:** Record the exact package/provider version, exact page, claim checked, result, and date. Separate documentation review from application build, integration, migration, and security-test evidence.
