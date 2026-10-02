# RBAC domain

**Client spec:** [`../../features/rbac.md`](../../features/rbac.md)  
**Permission catalog:** [`../../architecture/permissions.md`](../../architecture/permissions.md)  
**Tenancy:** [`../../architecture/multi-tenancy.md`](../../architecture/multi-tenancy.md)  
**Module:** `src/domains/rbac/`

## Purpose

Workspace-scoped **roles** mapped to **permission keys** within a **tenant**. Authoritative enforcement on Nest routes; JWT carries resolved permissions for the tenant/workspace the user signed into on their subdomain.

## Data model (target)

- `tenants` — customer org; unique `slug` (subdomain label)
- `workspaces` — hiring data container; `tenant_id` (1:1 primary workspace per tenant in v1)
- `permissions` — stable keys (`candidates.read`, `jobs.create`, …)
- `roles` — workspace-scoped (`workspace_id` + unique `name` per workspace)
- `role_permissions` — many-to-many
- `workspace_members` — user id, workspace id, `role_id` (NOT NULL). Migration `drizzle/0001_rbac.sql` adds `role_id` plus `roles`, `permissions`, and `role_permissions`.

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
