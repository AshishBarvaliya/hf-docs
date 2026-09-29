# Auth domain (API)

**Client spec:** [`../../features/auth.md`](../../features/auth.md)  
**ADR:** [`../../adr/003-auth-js-session.md`](../../adr/003-auth-js-session.md), [`../../adr/004-subdomain-multi-tenancy.md`](../../adr/004-subdomain-multi-tenancy.md)  
**Tenancy:** [`../../architecture/multi-tenancy.md`](../../architecture/multi-tenancy.md)  
**Module:** `src/domains/auth/` (to implement)

## Purpose

Validate Auth.js-issued JWTs on protected orchestration routes. Login (Credentials or Auth.js callback) validates **user + tenant slug** and issues JWT with `tenantId`, `workspaceId`, and later `permissions[]`.

## Plan

1. Shared `AUTH_SECRET` with Next Auth.js JWT strategy.
2. `tenants` + primary `workspaces` schema; dev seed for at least two tenant slugs.
3. Login endpoint: credentials + tenant slug → JWT; reject if user is not a `workspace_member` for that tenant’s workspace.
4. `JwtAuthGuard` (or global guard with `@Public()` opt-out) on domain controllers; attach `workspaceId` / `tenantId` to request context.
5. Protect `GET /api/v1/candidates` first; scope list by workspace; extend to new routes by default.
6. **RBAC (STEP 1.6)** — See `../rbac/README.md`: permission catalog, `@RequirePermission`, JWT `permissions[]`.
7. CORS allows tenant subdomain origins from client.

## Public API

- Guards exported for use in domain modules
- `@Public()` decorator for health checks

## References

- [permissions.md](../../architecture/permissions.md)
