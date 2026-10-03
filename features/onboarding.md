# Tenant onboarding (apex signup)

**Routes:** `(auth)/signup` (apex), `(auth)/auth/handoff` (tenant host), `POST /api/v1/auth/register`, `POST /api/v1/auth/handoff`, `GET /api/v1/tenants/:slug/status`  
**Roadmap step:** 1.7 (before STEP 5 ATS on isolated test environments)  
**ADR:** [004-subdomain-multi-tenancy.md](../adr/004-subdomain-multi-tenancy.md), [003-auth-js-session.md](../adr/003-auth-js-session.md)  
**Architecture:** [multi-tenancy.md](../architecture/multi-tenancy.md), [test-environment-onboarding.md](../architecture/test-environment-onboarding.md)

## Product behavior

On a **dedicated test apex** (env-configured, never hardcoded in code), a net-new visitor can:

1. Open the apex host (no tenant slug in the URL).
2. Complete **sign up** with name, email, password, company name, and desired **tenant slug**.
3. The system **provisions** tenant + primary workspace + admin user + default RBAC in one transaction.
4. The browser is sent to `{slug}.{APP_BASE_DOMAIN}` and establishes an **Auth.js session on that host** via a **single-use handoff** (no shared parent-domain cookie).
5. The user lands on `/overview` authenticated.

Unknown or inactive tenant subdomains still show **tenant-not-found**. Production main domain behavior is unchanged; this flow targets **isolated test stacks** (separate Vercel, API, Neon) documented in [test-environment-onboarding.md](../architecture/test-environment-onboarding.md).

## Plan

1. **Contract** — Public register + handoff + tenant status APIs; slug rules; global-unique email for new org signup (409 if email exists).
2. **Data** — `auth_handoff_codes` table; reuse `tenants`, `workspaces`, `users`, RBAC tables.
3. **Server** — Transactional provision; CORS allows apex + `https://*.{APP_BASE_DOMAIN}`; env `APP_BASE_DOMAIN`, `APP_PUBLIC_ORIGIN`.
4. **Client** — Apex signup UI; proxy resolves tenant via status API (optional `KNOWN_TENANT_SLUGS` dev override); handoff page + Auth.js credentials provider.
5. **Deploy** — Wildcard DNS/TLS on test apex only; migrations only (no `db:seed`) for clean-sheet validation.

## Dev

### Contract

| Method | Path | Auth | Body / response |
|--------|------|------|-----------------|
| `POST` | `/api/v1/auth/register` | Public | `{ email, password, name, companyName, tenantSlug }` → `{ tenant: { slug }, handoffToken }` |
| `POST` | `/api/v1/auth/handoff` | Public | `{ handoffToken, tenantSlug }` → same shape as login |
| `GET` | `/api/v1/tenants/:slug/status` | Public | `200 { slug, name, status: "active" }` or `404`; `name` is existing tenant branding |

Slug: lowercase DNS label, 3–63 chars, reserved labels rejected (`www`, `api`, `auth`, `admin`, …).

### Data

**`auth_handoff_codes`** (migration `0006_onboarding_handoff.sql`)

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `code_hash` | sha256 of opaque token |
| `user_id` | FK → `users.id` |
| `tenant_id` | FK → `tenants.id` |
| `expires_at` | timestamptz, ~5 minutes |
| `consumed_at` | nullable; set on successful exchange |
| `created_at` | timestamptz |

Reuses full `tenants` / `workspaces` / `users` / RBAC shapes per [persistence.md](../architecture/persistence.md).

### Server

- `server/src/domains/tenants/` — status endpoint, slug validation
- `server/src/domains/auth/` — register, handoff exchange
- `server/src/domains/tenants/workspace-provision.service.ts` — transactional bootstrap
- `server/src/config/cors-origins.ts` — apex + tenant subdomain origins from `APP_BASE_DOMAIN`

### Client

- `client/src/app/(auth)/signup/` — apex registration wizard
- `client/src/app/(auth)/auth/handoff/` — consumes handoff token on tenant host
- `client/src/lib/tenant/resolve-tenant.ts` — calls status API from proxy
- `client/src/lib/auth/register.ts`, `handoff.ts` — API clients
- Env: `APP_BASE_DOMAIN`, `NEXT_PUBLIC_API_ORIGIN`; `KNOWN_TENANT_SLUGS` optional dev shortcut only
- Visual treatment in `sprint-auth-visual-fidelity`: signup and handoff derive their spacing, card, footer, loading animation, and responsive behavior from `Login.html` because the design catalog has no signup/handoff mockup.

### Tests

| Area | Location |
|------|----------|
| Server unit | `tenant-slug.spec.ts`, `workspace-provision.service.spec.ts`, `auth.service.spec.ts` (login/tenant scope) |
| Server e2e | `test/onboarding.e2e-spec.ts` (register, sequential + concurrent handoff replay) |
| Client unit | `resolve-tenant.test.ts`, `gate.test.ts`, `register.test.ts` |
| Client e2e | `e2e/onboarding-signup.spec.ts` (when DB empty + register path) |

## Non-goals

- Email verification, billing, SSO, custom domains, invite others during signup.
- Cross-subdomain shared session cookies.
- Running `db:seed` on test/prod for customer tenants.

## Acceptance criteria

- [x] Apex `/signup` works without `KNOWN_TENANT_SLUGS` when API + DB are migrated empty.
- [x] Register creates tenant, workspace, admin role, and membership atomically.
- [x] Handoff is single-use and expires; replay returns 401; concurrent exchange yields at most one 200.
- [x] User arrives on tenant host with session and can open `/overview`.
- [x] Invalid/unknown slug → tenant-not-found; duplicate slug/email → 409.
- [x] Server and client tests above pass; lint clean.
- [x] Signup and handoff use the login-derived visual system (shared spacing, card, footer, responsive layout, and animated pending state).
