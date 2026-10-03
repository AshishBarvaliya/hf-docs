# Implementation task — Subdomain multi-tenancy foundation

**Status:** superseded — Origin binding shipped per [ADR 004 amendment](../../adr/004-amendment-tenant-origin-binding.md); remaining items tracked in sprint-ats-foundation.  
**Feature specs:** [auth.md](../../features/auth.md), [rbac.md](../../features/rbac.md), [workspace.md](../../features/workspace.md)  
**Architecture:** [multi-tenancy.md](../../architecture/multi-tenancy.md), [ADR 004](../../adr/004-subdomain-multi-tenancy.md)

## Goal

Each customer tenant is reachable at `{slug}.<APP_BASE_DOMAIN>`; login and API data are scoped to that tenant’s workspace.

## Server

- [ ] Drizzle: `tenants`, `workspaces` (FK tenant), link existing RBAC to workspace
- [ ] Seed: `acme` + `beta` tenants, workspaces, members, roles
- [ ] Login contract: credentials + tenant slug → JWT with `tenantId`, `workspaceId`, `permissions[]`
- [ ] Request context: resolve tenant from Origin; reject JWT/workspace mismatch
- [ ] Scope `GET /api/v1/candidates` (and all new routes) by `workspace_id`
- [ ] CORS: allow `https://*.app.example.com` (env-driven)
- [ ] Tests: cross-tenant isolation for list + 403 on wrong membership

## Client

- [ ] `src/lib/tenant/` — parse Host, base domain env
- [ ] Middleware: unknown tenant page; known tenant → auth flow
- [ ] Auth.js: pass tenant slug to login; session fields `tenantId`, `workspaceId`, `tenantSlug`
- [ ] Remove reliance on bare `localhost:3000` for production paths (document dev fallback)
- [ ] E2E smoke: two subdomains, two datasets

## Docs / env

- [ ] `APP_BASE_DOMAIN`, Auth wildcard notes in `client/.env.example` and `server/.env.example`

## Out of scope (this task)

- Custom domains, tenant self-signup, cross-tenant admin console
