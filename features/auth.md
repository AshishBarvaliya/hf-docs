# Authentication & session

**Routes:** `(auth)/login`, `(auth)/register` (optional v1), Auth.js route handler  
**Roadmap step:** 1.5 (before dashboard shell)  
**ADR:** [003-auth-js-session.md](../adr/003-auth-js-session.md), [004-subdomain-multi-tenancy.md](../adr/004-subdomain-multi-tenancy.md)  
**Architecture:** [multi-tenancy.md](../architecture/multi-tenancy.md)

## Product behavior

Each **customer tenant** uses its own **subdomain** (e.g. `https://acme.app.example.com`). Recruiters may sign in on that host **or** on the **global apex** login (`https://app.example.com/login`) with workspace + email + password; both land in the same recruiter app after authentication. Unauthenticated visitors on tenant app routes are redirected to login. Unknown subdomains show tenant-not-found; the apex host shows global sign-in (not tenant-not-found).

Authenticated sessions carry user identity, **tenant + workspace** context, and a bearer token for the orchestration API. Nest rejects missing or invalid tokens and never returns another tenant’s data.

## Plan

1. **Contract** — ADR 003 + 004: Auth.js on Next, JWT validated on Nest, shared secret; session/JWT fields include `userId`, `tenantId`, `tenantSlug`, `workspaceId`, and (with RBAC) `permissions[]`.
2. **Phase A (client)** — Tenant resolution from `Host` in middleware ([multi-tenancy.md](../architecture/multi-tenancy.md)); Auth.js config with subdomain-aware `AUTH_URL` / trusted hosts; login page on tenant host; middleware protecting non-public routes; session provider for client hooks.
3. **Phase B (server)** — `tenants` + primary `workspaces`; users + credentials; login validates **membership for the tenant slug** from the client; JWT guard on `GET /api/v1/candidates` scoped by `workspaceId`.
4. **Phase C (client API layer)** — Domain fetchers attach bearer from session; 401 → sign-out redirect.
5. **Phase D** — Hand off to [rbac.md](./rbac.md): permissions in JWT/session, server guards, client `<Can>` **before** STEP 2 shell.
6. **Phase E** — Workspace member UI (see [workspace.md](./workspace.md)) mutates RBAC membership; audit UI remains step 13.

## Dev

### Contract

| Area | Details |
|------|---------|
| Login API | `POST /api/v1/auth/login` — `tenantSlug`, `email`, `password`; JWT includes `userId`, `tenantId`, `tenantSlug`, `workspaceId`, `permissions[]` |
| Session | Auth.js `/api/auth/*`; encrypted session cookie on tenant host; `AUTH_SECRET` shared with Nest |
| Protected APIs | `Authorization: Bearer <accessToken>`; 401 without token; RBAC on domain routes ([rbac.md](./rbac.md)) |

### Client

- Auth.js: `src/auth.ts`, `src/app/api/auth/[...nextauth]/route.ts`
- Login: `src/app/(auth)/login/page.tsx` (no dashboard chrome)
- Middleware: `src/middleware.ts` — tenant from `Host`; redirect unauthenticated users
- `src/lib/tenant/` — `getTenantFromHost()`, `APP_BASE_DOMAIN`
- `src/lib/auth/session.ts` — `auth()`, `getAccessToken()` for fetchers
- Env: `AUTH_SECRET`, `AUTH_TRUST_HOST`, `APP_BASE_DOMAIN`, `KNOWN_TENANT_SLUGS`

### Server

- [`domains/server/auth/README.md`](../domains/server/auth/README.md) — `server/src/domains/auth/` JWT strategy, guard, tenant-scoped login
- Env: `AUTH_SECRET`, `CORS_ORIGIN` (tenant dev hosts)

### Data

Migration `server/drizzle/0002_foundation_schema_at_scale.sql` widens the auth tables. `0000` / `0001` stay as applied history. Login JSON does not return the new columns.

**`tenants`**

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `slug` | unique subdomain label |
| `name` | |
| `status` | `active` by default; login requires `active` |
| `created_at`, `updated_at` | |
| `created_by`, `updated_by` | nullable FK → `users.id` |
| `deleted_at` | soft delete; login ignores deleted tenants |

**`users`**

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `email` | unique |
| `name` | |
| `password_hash` | |
| `status` | `active` (default) or `disabled`. `disabled` cannot log in. Stored now; no disable-account API yet |
| `created_at`, `updated_at` | |
| `created_by`, `updated_by` | nullable FK → `users.id` (self) |
| `deleted_at` | soft delete; login ignores deleted users |

Membership, invites, and who invited someone live on `workspace_members` ([workspace.md](./workspace.md), [rbac.md](./rbac.md)). There is no session-revocation column: JWT expiry is the session policy until a spec names one. Audit events are a later table ([permissions-audit.md](./permissions-audit.md)); actor ids on these rows are the stored-now piece.

### Tests (required before sprint tasks marked done)

| Area | What to cover | Location |
|------|----------------|----------|
| **Server unit** | Login rejects wrong tenant; password failure; JWT payload shape | `server/src/domains/auth/**/*.spec.ts` |
| **Server API** | `POST /auth/login` success/401; `GET /candidates` 401 without bearer | `server/test/` or `*.e2e-spec.ts` |
| **Client unit** | `getTenantFromHost`, token attachment / session helper | `client/src/**/*.test.ts(x)` |
| **Client e2e** | Login on `acme.localhost` → authenticated candidates (when seed exists) | `client/e2e/` |

Track checkboxes: [`sprint/sprint-1-progress-checklist.md`](../sprint/sprint-1-progress-checklist.md) · Sprint tasks: `task-auth-client-tests`, `task-auth-client-e2e`, `task-auth-api-tests`.

- **i18n:** Login strings under `messages/` (`Auth` namespace).
- **Non-goals (v1):** SSO, MFA, invite flows (workspace phase); custom domains; org picker listing every membership without typing workspace slug on apex.

## Acceptance criteria

- [x] Known tenant subdomain loads app; unknown subdomain shows tenant-not-found (not another tenant’s data)
- [x] Unauthenticated tenant routes (e.g. `/`, `/candidates`) redirect to login on the same host
- [x] Successful login on `acme.*` establishes session with `tenantId` + `workspaceId` and lists only that tenant’s candidates
- [x] User valid on tenant A cannot log in on tenant B’s subdomain without membership there
- [x] Nest returns 401 without bearer token on protected candidates route
- [x] No secrets in client bundle except public Auth.js config
- [x] RBAC slice complete per [rbac.md](./rbac.md) before `(dashboard)` layout

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
