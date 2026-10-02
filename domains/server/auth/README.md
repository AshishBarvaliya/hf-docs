# Auth domain (API)

**Client spec:** [`../../features/auth.md`](../../features/auth.md)  
**ADR:** [`../../adr/003-auth-js-session.md`](../../adr/003-auth-js-session.md), [`../../adr/004-subdomain-multi-tenancy.md`](../../adr/004-subdomain-multi-tenancy.md)  
**Tenancy:** [`../../architecture/multi-tenancy.md`](../../architecture/multi-tenancy.md)  
**Module:** `src/domains/auth/`

## Purpose

Issue and validate JWTs for the orchestration API. Login checks **email + password + tenant slug** against `workspace_members` and returns a bearer token. `JwtAuthGuard` is global; `@Public()` opts out health and login.

## Contract (client must match)

`POST /api/v1/auth/login` — public. Body: `email`, `password`, `tenantSlug`.

Success **200**:

```json
{
  "accessToken": "<jwt>",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": { "id": "uuid", "email": "recruiter@acme.example", "name": "Acme Recruiter" },
  "tenant": { "id": "uuid", "slug": "acme" },
  "workspace": { "id": "uuid", "name": "Acme" },
  "permissions": [
    "analytics.read",
    "assessments.assign",
    "billing.manage",
    "candidates.edit",
    "candidates.read",
    "candidates.reject",
    "interviews.schedule",
    "jobs.create",
    "jobs.edit",
    "jobs.read",
    "settings.manage"
  ]
}
```

Failure is a generic **401** `{ "message": "Invalid credentials" }` for unknown email, bad password, unknown slug, or no membership on that tenant’s workspace. Invalid body is **400**.

**JWT:** HS256, signed with shared `AUTH_SECRET` (same value as Auth.js). Claims:

| Claim | Type |
|-------|------|
| `sub` | user id |
| `tenantId` | uuid |
| `tenantSlug` | string |
| `workspaceId` | uuid |
| `permissions` | string[] |

`iat` and `exp` are set. `expiresIn` is 3600 seconds.

`permissions` is the sorted key list for the user's workspace role. The acme/beta recruiter seed is `admin` and receives the full catalog above. `interviewer@acme.example` receives `["interviews.schedule"]` only.

`GET /api/v1/candidates` requires a bearer token and `candidates.read`, and filters by `workspaceId`. Missing or invalid token → **401** `{ "message": "Unauthorized" }`. Authenticated without `candidates.read` → **403** `{ "message": "Forbidden" }`.

## Dev seed (`npm run db:seed`)

| Email | Password | Tenant slug | Role |
|-------|----------|-------------|------|
| `recruiter@acme.example` | `dev-password` | `acme` | admin |
| `recruiter@beta.example` | `dev-password` | `beta` | admin |
| `interviewer@acme.example` | `dev-password` | `acme` | interviewer |

Each user is a member of only that tenant’s primary workspace. Each workspace has `admin` (full catalog, including `candidates.read`) and `interviewer` (`interviews.schedule` only). Acme candidates: Ada Lovelace, Grace Hopper. Beta: Katherine Johnson.

`workspace_members` stores `user_id`, `workspace_id`, `role_id`, and `status` (`active` for the seed). Login ignores soft-deleted tenants, workspaces, users, memberships, and roles, and requires `users.status`, `tenants.status`, and `workspace_members.status` to be `active`.

## Tables

Widened in `drizzle/0002_foundation_schema_at_scale.sql`. Full lists: [auth.md](../../features/auth.md) Dev → Data and [workspace.md](../../features/workspace.md).

- `tenants` — slug, name, status, audit columns, `deleted_at`
- `users` — email, name, password hash, `status` (`active` / `disabled`), audit columns, `deleted_at`
- `workspaces` — name plus company profile, culture, timezone, and posting defaults (stored now, not on the login JSON)

## Public API

- `JwtAuthGuard` registered as `APP_GUARD` (runs before `PermissionsGuard`)
- `@Public()` for routes that skip the JWT guard (health, login)
- `CurrentAuth()` param decorator — `sub`, `tenantId`, `tenantSlug`, `workspaceId`, `permissions`

## References

- [permissions.md](../../architecture/permissions.md)
