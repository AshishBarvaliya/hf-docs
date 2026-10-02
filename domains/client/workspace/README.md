# Workspace domain (client)

**Feature spec:** [workspace.md](../../../features/workspace.md)  
**Sprint guide:** [sprint-2-development-guide.md](../../../sprint/sprint-2-development-guide.md)  
**Code (planned):** `client/src/domains/workspace/`

## Purpose

Expose the signed-in user’s **current workspace** (from session `workspaceId` / tenant subdomain) to the rest of the app: company profile, culture, posting defaults, and member management UI.

## Surfaces

| Area | Routes | Mockups |
|------|--------|---------|
| Settings shell + sections | `(dashboard)/settings/...` | `designs/screens/SettingsGeneral.html`, `SettingsCompany.html`, `SettingsDefaults.html`, `SettingsCulture.html` |
| Culture read-only | Same culture route | `SettingsCultureReadOnly.html` |
| Members | `(dashboard)/settings/members`, invite | `SettingsMembers.html`, `SettingsInvite.html` |
| Roles | `(dashboard)/settings/roles` | `SettingsRoles.html` (see [rbac.md](../../../features/rbac.md)) |

## API consumption

| Hook / fetcher | Server |
|----------------|--------|
| `useWorkspace` | `GET /api/v1/workspaces/current`, `PATCH /api/v1/workspaces/:id` |
| `useMembers` | `GET/POST/PATCH /api/v1/workspaces/:id/members` |

Bearer token from session; TanStack Query for reads; invalidate workspace query after PATCH.

## Permissions

Use `settings.manage` from [permissions.md](../../../architecture/permissions.md) with `<Can>` — server enforces the same key.

## Tests

- Unit: `client/src/domains/workspace/**/*.test.ts(x)` — schemas, hooks
- E2e: settings route smoke on `acme.localhost` (see sprint 2 checklist)
