# RBAC (roles & permissions)

**Scope:** Cross-cutting — API enforcement + client gates  
**Roadmap step:** 1.6 (immediately after [auth.md](./auth.md); before dashboard shell)  
**Architecture:** [permissions.md](../architecture/permissions.md)  
**Server:** [`../domains/server/rbac/README.md`](../domains/server/rbac/README.md)  
**Tenancy:** [multi-tenancy.md](../architecture/multi-tenancy.md)

## Product behavior

Access is **permission-based**, not ad hoc role strings in UI or handlers. Each user has a **workspace role** within the **tenant they signed into** (subdomain). The backend maps roles → permission keys; JWT/session carries `tenantId`, `workspaceId`, and the resolved **permission list** for that workspace. UI uses `<Can permission="…">`; Nest uses `@RequirePermission('…')` (or equivalent guard) on workspace-scoped data.

Audit log UI remains [permissions-audit.md](./permissions-audit.md) (step 13); **RBAC foundation** must exist before product shell and domain features ship.

## Plan

1. **Permission catalog** — Canonical keys in `permissions.md`; shared constants on client; enum/table on server (single source documented in both repos).
2. **Phase A (server)** — Schema: `tenants`, `workspaces`, `roles`, `permissions`, `role_permissions`, `workspace_members` (user + workspace + role). Seed default roles per dev tenant (`acme`, `beta`).
3. **Phase B (server)** — `PermissionsGuard` after JWT auth; `403` when permission missing; attach permission set to request context from JWT claims or DB lookup.
4. **Phase C (auth integration)** — JWT includes `tenantId`, `workspaceId`, `role`, `permissions[]` (or compact role id resolved server-side). Auth.js session exposes same fields to client. Login is rejected if user is not a member of the workspace for the request tenant.
5. **Phase D (client)** — `src/shared/permissions/`: `PERMISSIONS` constants, `usePermissions()`, `<Can>`, optional `requirePermission()` for server actions.
6. **Phase E (with STEP 2 shell)** — Sidebar/nav filtered by `*.read` permissions; no dashboard build without RBAC hooks wired.
7. **Incremental** — Each new domain route/mutation adds server permission check + client `<Can>` in the same slice (not a step-13 batch).

## Dev

| Piece | Location |
|-------|----------|
| Client module | `src/shared/permissions/` — `constants.ts`, `use-permissions.ts`, `can.tsx` |
| Session | Auth.js callbacks merge `permissions` from login/API into session |
| Server module | `server/src/domains/rbac/` — catalog, membership service, guard |
| Decorator | `@RequirePermission('candidates.read')` on controllers/handlers |

### Tests (required before sprint tasks marked done)

| Area | What to cover | Location |
|------|----------------|----------|
| **Server unit** | Role → permission matrix; `PermissionsGuard` with missing key | `server/src/domains/rbac/**/*.spec.ts` |
| **Server API** | `GET /candidates` 403 without `candidates.read`; 200 with permission | `server/test/` e2e |
| **Client unit** | `usePermissions`, `<Can>` render/hide children | `client/src/shared/permissions/**/*.test.tsx` |
| **Cross-tenant** | JWT for tenant A cannot read tenant B workspace data | server e2e (+ client e2e when harness ready) |

Track checkboxes: [`sprint/sprint-1-progress-checklist.md`](../sprint/sprint-1-progress-checklist.md) · Sprint tasks: `task-rbac-client-tests`, `task-rbac-api-tests`.

- **Never** `user.role === "admin"` in feature code (client or server domain logic).
- **Workspace** member invite/role UI extends RBAC in [workspace.md](./workspace.md); RBAC schema lands first.

## Acceptance criteria

- [x] Documented permission keys match enforced keys on server
- [x] JWT/session includes `tenantId`, `workspaceId`, and permission list for the subdomain used at login
- [x] Cross-tenant isolation: token for tenant A cannot read tenant B data (test with two seeded subdomains)
- [x] `GET /api/v1/candidates` requires `candidates.read`
- [x] `<Can>` and `usePermissions()` available before `(dashboard)` layout ships
- [x] At least two seeded roles (e.g. admin vs interviewer) provable in tests

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/SettingsRoles.html` | Roles & permissions matrix |
| `designs/screens/NoAccess.html` | Blocked action / page |
| `designs/screens/JobsViewer.html` | Jobs list without edit |
| `designs/screens/JobViewer.html` | Job workspace read-only |
| `designs/screens/ProfileHM.html` | Hiring Manager–limited profile |
| `designs/screens/SettingsCultureReadOnly.html` | Culture settings read-only (e.g. Recruiter) |

## References

- [multi-tenancy.md](../architecture/multi-tenancy.md)
- [auth.md](./auth.md)
- [workspace.md](./workspace.md)
- [permissions-audit.md](./permissions-audit.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
