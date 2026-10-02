# Permissions & audit

**Scope:** Cross-cutting — all routes and actions  
**Roadmap step:** 13 for **audit log + polish**; core **RBAC** is STEP 1.6 ([rbac.md](./rbac.md), before dashboard shell)  
**Domains:** `workspace` + `src/shared/permissions/`

## Product behavior

**Role-based access** via permission keys (not hardcoded role strings). UI uses `<Can permission="…">` and route guards. **Audit** surfaces who changed job, rejected candidate, edited workflow—read from audit API (admin/recruiter views as permitted).

## Plan

1. **Prerequisite** — [rbac.md](./rbac.md) (STEP 1.6): permission catalog, JWT claims, `PermissionsGuard`, `usePermissions`, `<Can>`.
2. **Phase A (per feature slice)** — Gate new actions with `<Can>` + server `@RequirePermission` in the same delivery cycle.
3. **Phase B (STEP 2 shell)** — Nav filtered by `*.read` permissions.
4. **Phase C (step 13)** — Audit log viewer for admins (filters: actor, entity, date).
5. **Phase D** — Polish: consistent forbidden states, no leaky actions in DOM for unauthorized users.
6. **Dependencies** — Backend must enforce all mutations; client is UX only.

## Dev

| Piece | Implementation |
|-------|----------------|
| Module | `src/shared/permissions/can.tsx`, `use-permissions.ts`, permission constants file |
| Server | Route handlers / server actions call `requirePermission` when added |
| Audit | `domains/workspace/` or `domains/audit/` — `useAuditLog` + table |
| Nav | Filter sidebar items in dashboard layout |

- **Never** `user.role === "admin"` in feature code ([permissions-frontend.mdc](../../.cursor/rules/permissions-frontend.mdc)).
- **Tests:** Permission matrix unit tests; E2E as role fixtures when test auth exists.

## Acceptance criteria

- [ ] All destructive actions wrapped in `<Can>`
- [ ] Sidebar reflects read permissions
- [ ] Audit log page for authorized roles
- [ ] Documented permission list stays in sync with [permissions.md](../architecture/permissions.md)

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/NoAccess.html` | Permission denied in-app |
| `designs/screens/ProfileActivity.html` | Activity feed (audit-adjacent; no standalone audit log page) |

Roles UI: `designs/screens/SettingsRoles.html` ([rbac.md](./rbac.md)).

## References

- [workspace.md](./workspace.md)
- [architecture/permissions.md](../architecture/permissions.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
