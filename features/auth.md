# Authentication & session

**Routes:** `(auth)/login`, `(auth)/register` (optional v1), Auth.js route handler  
**Roadmap step:** 1.5 (before dashboard shell)  
**ADR:** [003-auth-js-session.md](../adr/003-auth-js-session.md), [004-subdomain-multi-tenancy.md](../adr/004-subdomain-multi-tenancy.md)  
**Architecture:** [multi-tenancy.md](../architecture/multi-tenancy.md)

## Product behavior

Each **customer tenant** uses its own **subdomain** (e.g. `https://acme.app.example.com`). Recruiters open their company URL, then sign in on that host. Unauthenticated visitors on tenant app routes are redirected to login. Unknown subdomains show a tenant-not-found experience (not a shared login).

Authenticated sessions carry user identity, **tenant + workspace** context, and a bearer token for the orchestration API. Nest rejects missing or invalid tokens and never returns another tenant’s data.

## Plan

1. **Contract** — ADR 003 + 004: Auth.js on Next, JWT validated on Nest, shared secret; session/JWT fields include `userId`, `tenantId`, `tenantSlug`, `workspaceId`, and (with RBAC) `permissions[]`.
2. **Phase A (client)** — Tenant resolution from `Host` in middleware ([multi-tenancy.md](../architecture/multi-tenancy.md)); Auth.js config with subdomain-aware `AUTH_URL` / trusted hosts; login page on tenant host; middleware protecting non-public routes; session provider for client hooks.
3. **Phase B (server)** — `tenants` + primary `workspaces`; users + credentials; login validates **membership for the tenant slug** from the client; JWT guard on `GET /api/v1/candidates` scoped by `workspaceId`.
4. **Phase C (client API layer)** — Domain fetchers attach bearer from session; 401 → sign-out redirect.
5. **Phase D** — Hand off to [rbac.md](./rbac.md): permissions in JWT/session, server guards, client `<Can>` **before** STEP 2 shell.
6. **Phase E** — Workspace member UI (see [workspace.md](./workspace.md)) mutates RBAC membership; audit UI remains step 13.

## Dev

| Piece | Location |
|-------|----------|
| Auth.js | `src/auth.ts`, `src/app/api/auth/[...nextauth]/route.ts` (or v5 equivalent path per Auth.js docs) |
| Login UI | `src/app/(auth)/login/page.tsx` — form only; no dashboard chrome |
| Middleware | `src/middleware.ts` — resolve tenant from Host; redirect unauthenticated users |
| Tenant helpers | `src/lib/tenant/` — `getTenantFromHost()`, `APP_BASE_DOMAIN` |
| Session helpers | `src/lib/auth/session.ts` — `auth()`, `getAccessToken()` for server/client fetchers |
| Server | `server/src/domains/auth/` — JWT strategy, guard, tenant-scoped login |
| Env | `AUTH_SECRET`, `AUTH_URL`, `APP_BASE_DOMAIN` (client); same secret on server; CORS for tenant origins |

### Tests (required before sprint tasks marked done)

| Area | What to cover | Location |
|------|----------------|----------|
| **Server unit** | Login rejects wrong tenant; password failure; JWT payload shape | `server/src/domains/auth/**/*.spec.ts` |
| **Server API** | `POST /auth/login` success/401; `GET /candidates` 401 without bearer | `server/test/` or `*.e2e-spec.ts` |
| **Client unit** | `getTenantFromHost`, token attachment / session helper | `client/src/**/*.test.ts(x)` |
| **Client e2e** | Login on `acme.localhost` → authenticated candidates (when seed exists) | `client/e2e/` |

Track checkboxes: [`sprint/sprint-1-progress-checklist.md`](../sprint/sprint-1-progress-checklist.md) · Sprint tasks: `task-auth-client-tests`, `task-auth-api-tests`.

- **i18n:** Login strings under `messages/` (`Auth` namespace).
- **Non-goals (v1):** SSO, MFA, invite flows (workspace phase); custom domains; recruiter app on bare root domain without tenant slug.

## Acceptance criteria

- [ ] Known tenant subdomain loads app; unknown subdomain shows tenant-not-found (not another tenant’s data)
- [ ] Unauthenticated tenant routes (e.g. `/`, `/candidates`) redirect to login on the same host
- [ ] Successful login on `acme.*` establishes session with `tenantId` + `workspaceId` and lists only that tenant’s candidates
- [x] User valid on tenant A cannot log in on tenant B’s subdomain without membership there
- [x] Nest returns 401 without bearer token on protected candidates route
- [ ] No secrets in client bundle except public Auth.js config
- [ ] RBAC slice complete per [rbac.md](./rbac.md) before `(dashboard)` layout

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/Login.html` | Sign in |
| `designs/screens/LoginError.html` | Wrong email or password |
| `designs/screens/LoginBusy.html` | Submitting |
| `designs/screens/LoginExpired.html` | Session ended (401) |
| `designs/screens/TenantNotFound.html` | Unknown subdomain |
| `designs/screens/NoAccess.html` | In-app 403 (pairs with [rbac.md](./rbac.md)) |

## References

- [multi-tenancy.md](../architecture/multi-tenancy.md)
- [workspace.md](./workspace.md)
- [permissions-audit.md](./permissions-audit.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
