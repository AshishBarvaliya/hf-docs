# ADR 004 amendment — Origin binding for tenant JWT

## Status

Accepted — 2026-10-05

## Context

ADR [004](./004-subdomain-multi-tenancy.md) requires guards to reject tokens whose tenant does not match the request’s resolved tenant context. Bearer tokens alone can be replayed from another allowed origin if `Origin` is not checked.

## Decision

- **Isolation model:** **Application-level** filtering on `workspace_id` / `tenant_id` in domain services. Postgres **RLS is not** required in v1 (clarifies [aws-platform.md](../architecture/aws-platform.md) wording).
- **`TenantContextGuard`:** After JWT validation, when `Origin` is present and parses to a tenant slug (see `server/src/config/tenant-origin.ts`), it must equal JWT `tenantSlug` or the API returns **403**.
- **Missing `Origin`:** Allowed (CLI, some tests); rely on workspace scoping in services.
- **Apex hosts** (no tenant slug in origin): skip binding; used for signup only.

## Consequences

- E2e may add `Origin` headers when testing cross-tenant replay.
- Planning task [2026-09-29-subdomain-multi-tenancy.md](../planning/implementation-tasks/2026-09-29-subdomain-multi-tenancy.md) superseded for Origin binding; login + workspace scope remain as implemented.
