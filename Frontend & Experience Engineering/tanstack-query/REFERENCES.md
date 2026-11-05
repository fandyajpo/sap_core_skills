# TanStack Query Reference Notes

This supplementary note captures SAP Core-specific cache decisions; the skill entry point remains [SKILL.md](SKILL.md).

## Query identity
A query key should encode every input that changes the result: module/resource, normalized filter, sort, page/cursor, and company scope when relevant. Do not include secrets. Treat list and detail keys as separate resources so mutations can invalidate narrowly or broadly as required.

## Mutation and cache policy
- Financial posting, stock movement, approval, and document transitions are server-authoritative. Prefer invalidation/refetch unless a deterministic optimistic rollback is proven safe.
- Mutations that are retried require an API idempotency contract; client retry settings alone do not make a write idempotent.
- Sign-out and company-context change must remove or segregate protected cache data according to the application's security policy.
- Query caching is not a replacement for HTTP/server authorization, data minimization, or audit.

## Official documentation
- Overview: https://tanstack.com/query/latest/docs/framework/react/overview
- Query keys: https://tanstack.com/query/latest/docs/framework/react/guides/query-keys
- Mutations: https://tanstack.com/query/latest/docs/framework/react/guides/mutations

**Version verification:** package versions were not supplied. The `latest` URLs are moving references; verify signatures and defaults against the locked package version before coding.
