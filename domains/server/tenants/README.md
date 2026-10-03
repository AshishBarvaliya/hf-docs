# Server domain — tenants & provisioning

Mirrors `server/src/domains/tenants/`.

## Public APIs

| Route | Purpose |
|-------|---------|
| `GET /api/v1/tenants/:slug/status` | Whether slug is an active tenant (for client host gate) |
| `POST /api/v1/auth/register` | Apex signup + provision (auth module) |
| `POST /api/v1/auth/handoff` | Exchange one-time token for login JWT (auth module) |

## Provisioning

`WorkspaceProvisionService` creates in one transaction:

- `tenants` row (`active`)
- primary `workspaces` row
- `users` row (new email only)
- system `roles` (admin, interviewer) + `role_permissions`
- `workspace_members` (admin)

Ensures global `permissions` catalog rows exist (upsert by key).

## Slug rules

See `tenant-slug.ts` — DNS label regex, length 3–63, reserved list.

## Related

- [onboarding.md](../../../features/onboarding.md)
- [auth/README.md](../auth/README.md)
