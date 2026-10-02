# RBAC domain

**Client spec:** [`../../features/rbac.md`](../../features/rbac.md)  
**Permission catalog:** [`../../architecture/permissions.md`](../../architecture/permissions.md)  
**Tenancy:** [`../../architecture/multi-tenancy.md`](../../architecture/multi-tenancy.md)  
**Module:** `src/domains/rbac/`

## Purpose

Workspace-scoped **roles** mapped to **permission keys** within a **tenant**. Authoritative enforcement on Nest routes; JWT carries resolved permissions for the tenant/workspace the user signed into on their subdomain.

## Data model

Migration `drizzle/0001_rbac.sql` created the RBAC tables. `drizzle/0002_foundation_schema_at_scale.sql` widens them. Column lists: [rbac.md](../../features/rbac.md) Dev → Data.

- `tenants` — customer org; unique `slug` (subdomain label); `status`, actor columns, `deleted_at`
- `workspaces` — hiring data container; `tenant_id` (1:1 primary workspace per tenant in v1); company profile and culture columns stored for settings later
- `permissions` — stable keys (`candidates.read`, `jobs.create`, …) plus `updated_at` and actor columns. Catalog exception: no `deleted_at`; hard-delete only when nothing references the key
- `roles` — workspace-scoped (`workspace_id` + unique `name`); `description`, `is_system`, actor columns, `deleted_at`
- `role_permissions` — role + permission, with `created_at` and `created_by`
- `workspace_members` — user, workspace, `role_id` (NOT NULL), `status` (`active` / `invited` / `removed`), `invited_by`, actor columns, `deleted_at`. Login requires `status = active` and `deleted_at` null

Dev seed: each of `acme` and `beta` has `admin` (full catalog from `permissions.md`) and `interviewer` (`interviews.schedule` only). Recruiters are `admin`. `interviewer@acme.example` is the acme interviewer.

## API behavior

- After `JwtAuthGuard`, `PermissionsGuard` checks `@RequirePermission(...)` metadata.
- `401` unauthenticated; `403` authenticated but missing permission or wrong tenant/workspace context.
- All entity queries filter by authenticated `workspace_id`.
- Membership changes (workspace feature) invalidate or refresh JWT per session policy documented in client auth spec.

## Delivery order

1. Tenant + workspace + RBAC schema + seed (two dev tenants, multiple roles)
2. Guard + decorator on existing `GET /candidates` (workspace-scoped)
3. Expose permissions on tenant-scoped login/session endpoint for Auth.js

## References

- `../auth/README.md`
- Client [permissions-audit.md](../../features/permissions-audit.md) (audit UI later)
