# ADR 004 — Subdomain multi-tenancy

## Status

Accepted — 2026-09-29

## Context

Hyreefy serves **many customer companies**. Each customer’s recruiters must only see that company’s jobs, candidates, and settings. We need a clear tenant boundary before auth, RBAC, and workspace settings scale across production customers.

## Decision

- **Tenant** = one paying customer organization (isolation and billing boundary).
- **URL routing:** Each tenant gets a dedicated **subdomain** on a shared app host, e.g. `https://{tenantSlug}.app.example.com` (exact base domain is env-configured).
- **Workspace (v1):** One **primary workspace** per tenant; `workspaceId` in JWT/session is always scoped to the tenant resolved from the request host. Multi-workspace *inside* one tenant is out of scope for v1.
- **Login flow:** Sign-in happens **on the tenant subdomain**. Credentials (or future SSO) are validated together with the **tenant slug from the Host header**. A user who is not a member of that tenant’s workspace cannot authenticate on that subdomain, even if they exist globally.
- **API:** Nest remains authoritative. JWT carries `tenantId`, `workspaceId`, and `permissions[]`. Every data query filters by `workspace_id`; guards reject tokens whose tenant does not match the request’s resolved tenant context (see [multi-tenancy.md](../architecture/multi-tenancy.md)).
- **Client:** Next.js middleware resolves tenant from `Host` before auth redirects and passes tenant context into Auth.js login / session callbacks.

## Consequences

- Auth.js `AUTH_URL` / trusted hosts must allow wildcard subdomains in production (document in `.env.example`).
- CORS on the API must allow origins matching `https://*.app.example.com` (or explicit tenant list in dev).
- Local dev uses `{slug}.localhost:3000` or documented env override; spike `localhost:3000` without a tenant slug is **dev-only** and not a production entry pattern.
- RBAC `workspace_members` rows are always tied to a workspace that belongs to exactly one tenant.
- STEP 3 workspace settings UI edits **the current tenant’s** workspace (branding, members, roles)—not a global admin console on a bare root domain.

## Non-goals (v1)

- Custom domains (customer CNAME to their own hostname).
- Self-service tenant signup on a marketing root domain (provisioning is ops/admin or a later onboarding product).
- Cross-tenant super-admin UI in the recruiter app.
- Candidate-facing career sites on tenant subdomains (recruiter app only in v1).

## Alternatives considered

- **Path-based tenancy** (`/t/acme/...`) — rejected; product wants branded subdomain per customer and simpler cookie/session isolation per host.
- **Tenant id in query string only** — rejected; easy to leak or swap; host-based resolution is the product contract.
- **Separate deploy per customer** — rejected for cost and velocity; shared app + row-level workspace isolation.

## References

- [multi-tenancy.md](../architecture/multi-tenancy.md)
- [auth.md](../features/auth.md)
- [workspace.md](../features/workspace.md)
- [rbac.md](../features/rbac.md)
- [003-auth-js-session.md](./003-auth-js-session.md)
