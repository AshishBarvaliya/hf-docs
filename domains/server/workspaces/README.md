# Workspaces domain (API)

**Feature spec:** [`../../features/workspace.md`](../../features/workspace.md)  
**Persistence:** `workspaces` columns in `server/drizzle/0002_foundation_schema_at_scale.sql`  
**Module (planned):** `server/src/domains/workspaces/`

## Purpose

Expose the authenticated user’s **primary workspace** for the tenant they signed into: company profile, culture fields, posting defaults, and member management. All routes are JWT-scoped by `workspaceId` from the token.

## Contract (target — sprint 2)

| Method | Path | Permission (typical) |
|--------|------|----------------------|
| `GET` | `/api/v1/workspaces/current` | JWT; workspace from token |
| `PATCH` | `/api/v1/workspaces/:id` | `settings.manage`; `:id` === JWT `workspaceId` |
| `GET` | `/api/v1/workspaces/:id/members` | `settings.manage`; paginated (`limit` default 50, max 100) |
| `POST` | `/api/v1/workspaces/:id/members` | `settings.manage` — invite (`status: invited`, `invited_by`) |
| `PATCH` | `/api/v1/workspaces/:id/members/:memberId` | `settings.manage` — role / status |

Zod DTOs at HTTP boundary; `PermissionsGuard` after `JwtAuthGuard`; filter `deleted_at IS NULL`; set `updated_by` on workspace PATCH.

**Sprint guide:** [sprint-2-development-guide.md](../../sprint/sprint-2-development-guide.md)

## Data

See **Dev → Data** in [workspace.md](../../features/workspace.md) for the full `workspaces` column list.

## Tests

- Unit: patch validation, scope checks
- E2e: cross-tenant `workspaceId` rejected; 403 without settings permission
