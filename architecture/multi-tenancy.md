# Multi-tenancy — subdomains, tenants, and workspaces

Hyreefy is **multi-tenant**: many customer companies share one deployment. Each customer is a **tenant** reached at its own **subdomain**. Recruiting data is isolated in that tenant’s **workspace** (1:1 with tenant in v1).

**Decision record:** [ADR 004](../adr/004-subdomain-multi-tenancy.md)

## Terms

| Term | Meaning |
|------|---------|
| **Tenant** | Customer organization; billing and isolation boundary |
| **Tenant slug** | DNS label, e.g. `acme` in `acme.app.example.com` |
| **Workspace** | Company hiring context inside a tenant (jobs, candidates, members, RBAC) |
| **Member** | User account linked to a workspace via `workspace_members` |

```text
Tenant (acme)
  └── Workspace (primary)
        ├── workspace_members → users + roles
        ├── jobs, candidates, …
        └── RBAC permissions
```

## Request flow (recruiter app)

```text
Browser: https://acme.app.example.com/candidates
                    │
                    ▼
        Next middleware: parse Host → tenantSlug = "acme"
                    │
        ┌───────────┴───────────┐
        ▼                       ▼
  Unknown slug            Active tenant (API status)
  → tenant-not-found      → optional dev `KNOWN_TENANT_SLUGS` override
        │                       │
        │                       ▼
        │               Auth required routes
        │               → login on same host
        │                       │
        │                       ▼
        │               Session/JWT: tenantId, workspaceId,
        │               permissions[], userId
        │                       │
        └───────────────────────┴──► API calls with Bearer JWT
                                    Nest: verify JWT + scope queries
                                    to workspace_id
```

### Rules

1. **Host defines tenant context for the UI** — recruiters use their company subdomain; **apex signup** on isolated test stacks provisions a new tenant ([onboarding.md](../features/onboarding.md)).
2. **JWT defines tenant context for the API** — subdomain alone is not sent as the sole authorization signal; the token must include `tenantId` / `workspaceId` and guards enforce consistency with the request origin where applicable.
3. **No cross-tenant data** — list/detail/mutation handlers always filter by the authenticated workspace (and thus tenant).
4. **Same email, different tenants** — a consultant may have memberships on `acme.` and `beta.` subdomains; each login is independent per host.

## Authentication integration

| Step | Behavior |
|------|----------|
| Login page | Rendered on `{slug}.app…`; slug from Host |
| Credentials POST | Nest (or Auth.js callback calling Nest) receives email/password **and** tenant slug |
| Success | Issue JWT with `tenantId`, `workspaceId`, `permissions[]`, `sub` (user id) |
| Failure | Generic error (no hint whether email exists on another tenant) |
| Session | Auth.js session mirrors JWT fields for client hooks |

See [auth.md](../features/auth.md) and [rbac.md](../features/rbac.md).

## Data model (server target)

Add to Drizzle schema (names illustrative):

- `tenants` — `id`, `slug` (unique), `name`, `status`, timestamps
- `workspaces` — `id`, `tenant_id` (unique per primary workspace v1), `name`, …
- Existing RBAC tables scope `roles` / `workspace_members` to `workspaces.id`

Seed at least two tenants in dev (`acme`, `beta`) with distinct subdomains and users.

## Client implementation map

| Piece | Responsibility |
|-------|----------------|
| `middleware.ts` | Parse subdomain; block unknown tenant; attach tenant slug to headers/cookies for downstream |
| `src/lib/tenant/` | `getTenantFromHost(host)`, constants for base domain |
| Auth.js callbacks | Include `tenantId`, `workspaceId`, `tenantSlug` in session |
| Domain `api/` fetchers | Bearer from session; optional `Origin` already identifies tenant to API |
| Branding (later STEP 3) | Logo/name from workspace settings keyed by current tenant |

## Server implementation map

| Piece | Responsibility |
|-------|----------------|
| `TenantModule` / middleware | Resolve tenant from `Origin` or trusted header; attach to request context |
| Auth login | Validate membership for `(user, tenant)` |
| JWT strategy | Validate claims; expose `req.user.workspaceId` |
| Domain services | All queries `where workspace_id = :ctx` |
| CORS | Allow tenant subdomains |

## Environments

| Environment | URL pattern |
|-------------|-------------|
| Production | `https://{slug}.app.example.com` |
| Staging | `https://{slug}.staging.example.com` |
| Local | `http://{slug}.localhost:3031` or documented `DEV_TENANT_SLUG` fallback for spike migration |

Env vars (document in both `.env.example` files):

- `APP_BASE_DOMAIN` — e.g. `app.example.com` (client: derive subdomain)
- `API_PUBLIC_URL` — Nest base URL (may be shared across tenants)
- Auth.js wildcard trust / `AUTH_URL` pattern for subdomains

## UX edge cases

- **Wrong subdomain after invite link** — invite emails must use the tenant’s full subdomain URL.
- **Bookmark to old slug** — after tenant rename (rare), redirect policy is ops-defined; v1 may disallow slug changes.
- **Root domain `app.example.com`** — marketing or “find your workspace” page only; not the authenticated recruiter shell (non-goal: full product on bare host).

## Testing

- E2E: login on `acme.localhost`, assert candidates from acme seed only; same user denied on `beta.localhost` unless seeded there.
- API: JWT for tenant A cannot read tenant B workspace ids (403/404, no leakage).

## Related docs

- [permissions.md](./permissions.md) — permissions are workspace-scoped
- [routing-and-shell.md](./routing-and-shell.md) — shell on tenant host
- [server/overview.md](./server/overview.md) — API conventions
- [workspace.md](../features/workspace.md) — settings for current tenant workspace
