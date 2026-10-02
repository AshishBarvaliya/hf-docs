# Sprint 2 — development guide

**Sprint:** `sprint-2` (2026-10-03 → 2026-10-25)  
**Trackers:** [`client/current.yaml`](./client/current.yaml) · [`server/current.yaml`](./server/current.yaml) · [`sprint-2-progress-checklist.md`](./sprint-2-progress-checklist.md)  
**Feature spec:** [workspace.md](../features/workspace.md)  
**Architecture:** [multi-tenancy.md](../architecture/multi-tenancy.md), [persistence.md](../architecture/persistence.md), [permissions.md](../architecture/permissions.md)  
**Implementation task:** [workspace-step-3](../planning/implementation-tasks/2026-10-03-workspace-step-3.md)

**Goal:** Ship roadmap **STEP 3 — Workspace** end-to-end: session/workspace context, settings APIs, settings UI (mockup-aligned), members list + invite + role change. Client and server stay paired per [product-delivery-principles.mdc](../.cursor/rules/product-delivery-principles.mdc).

---

## Scope summary

| In sprint 2 (STEP 3) | Out of sprint 2 (later steps / specs) |
|----------------------|----------------------------------------|
| Phase A — `WorkspaceProvider`, hooks, types, fetchers | Phase D — analyzer settings gallery ([ai-skill-analyzers.md](../features/ai-skill-analyzers.md)) |
| Phase B — `(dashboard)/settings/*` (General, Company, Defaults, Culture) | Billing UI, branding asset upload, `default_pipeline_template_id` ([hiring-workflows.md](../features/hiring-workflows.md)) |
| Phase C — Members, invite, role assignment UI | Self-service tenant signup / provisioning product |
| Server — `GET/PATCH` workspace, members APIs, guards, tests | Transactional **email** for invites (v1: invite link + membership row; email product in [emails.md](../features/emails.md)) |
| Cross-tenant + 403 test matrix | Jobs, ATS, profile (STEP 4+) — listed in client YAML but **not** sprint focus until STEP 3 checklist is done |

**Data:** No new migration for workspace profile columns — use `server/drizzle/0002_foundation_schema_at_scale.sql` (already specifies full `workspaces` + `workspace_members` shape).

**Design reference:** All `designs/screens/Settings*.html` files exist; index in [product-design-mockups.md](../architecture/product-design-mockups.md) and [workspace.md](../features/workspace.md#design-mockups).

---

## Delivery order (recommended)

1. **Server** `GET /api/v1/workspaces/current` + Zod response + unit/e2e (unblocks client hooks).
2. **Client** Phase A — domain module + `WorkspaceProvider` + `useWorkspace` (TanStack Query).
3. **Server** `PATCH` + `settings.manage` guard; set `updated_by` on write.
4. **Client** Phase B — settings layout + one section at a time (Zod per form); gate edit with `<Can permission="settings.manage">`; read-only culture for roles without permission ([SettingsCultureReadOnly.html](../../designs/screens/SettingsCultureReadOnly.html)).
5. **Server** members list (paginated) + invite + role `PATCH`; enforce `workspaceId` from JWT matches `:id`.
6. **Client** Phase C — members pages; invite URL uses **tenant subdomain** from session.
7. **Tests** — checklist rows + recalc YAML `progress`.

---

## Routes (client)

Settings use dashboard chrome ([design-system-shell.md](../features/design-system-shell.md)). Paths are illustrative; match App Router conventions used in `(dashboard)/`.

| Mockup | Suggested App Router path | Permission (typical) |
|--------|---------------------------|----------------------|
| `SettingsGeneral.html` | `(dashboard)/settings/general` | `settings.manage` to edit |
| `SettingsCompany.html` | `(dashboard)/settings/company` | `settings.manage` |
| `SettingsDefaults.html` | `(dashboard)/settings/defaults` | `settings.manage` |
| `SettingsCulture.html` | `(dashboard)/settings/culture` | `settings.manage` |
| `SettingsCultureReadOnly.html` | same route, read-only branch | view without `settings.manage` |
| `SettingsMembers.html` | `(dashboard)/settings/members` | `settings.manage` |
| `SettingsInvite.html` | `(dashboard)/settings/members/invite` | `settings.manage` |
| `SettingsRoles.html` | `(dashboard)/settings/roles` | `settings.manage` + [rbac.md](../features/rbac.md) |

Nav: add Settings entry in sidebar when shell exists; hide or disable sections the user cannot edit.

---

## API contract (server)

Canonical permission key: **`settings.manage`** ([permissions.md](../architecture/permissions.md), `server/src/domains/rbac/permission-catalog.ts`).

| Method | Path | Auth | Permission |
|--------|------|------|------------|
| `GET` | `/api/v1/workspaces/current` | JWT | member of `workspaceId` in token |
| `PATCH` | `/api/v1/workspaces/:id` | JWT | `settings.manage`; `:id` **must equal** token `workspaceId` |
| `GET` | `/api/v1/workspaces/:id/members` | JWT | `settings.manage` |
| `POST` | `/api/v1/workspaces/:id/members` | JWT | `settings.manage` (invite) |
| `PATCH` | `/api/v1/workspaces/:id/members/:memberId` | JWT | `settings.manage` (role / status) |

**`GET /workspaces/current`** — return profile, culture, and posting-default fields from `workspaces` (see feature **Dev → Data**). Omit internal actor columns from JSON unless needed.

**`PATCH`** — partial body per section; Zod at boundary; reject unknown `workspaceId`; filter `deleted_at IS NULL`.

**Members `GET`** — query `?page=` & `?limit=` (default `limit=50`, max `100`); sort by `created_at` desc; include user display fields + role name + `status`.

**Invite `POST`** — create or reactivate `workspace_members` with `status: invited`, set `invited_by` / `created_by`; v1 response includes **invite link** on tenant host (no email worker required in this slice).

**Errors:** 401 unauthenticated; 403 missing permission or workspace id mismatch; 404 unknown member; consistent API error shape (no stack traces).

**CORS:** Nest allows comma-separated `CORS_ORIGIN` plus `*.localhost` origins in non-production (`server/src/config/cors-origins.ts`) so tenant subdomains can call the API from the browser.

Module: `server/src/domains/workspaces/` — see [domains/server/workspaces/README.md](../domains/server/workspaces/README.md).

---

## Client modules

```text
client/src/domains/workspace/
  api/           # fetchers (bearer from session)
  hooks/         # useWorkspace, useMembers, useUpdateWorkspace
  schemas/       # Zod aligned with server PATCH bodies
  components/    # settings sections (optional split by screen)
```

Wire `WorkspaceProvider` in app providers after session is available; prefer **TanStack Query** for `GET current` with modest `staleTime` (e.g. 60s) — workspace settings change infrequently; invalidate on successful PATCH.

---

## Production readiness (this slice)

| Area | Expectation |
|------|-------------|
| **AuthZ** | `JwtAuthGuard` + `PermissionsGuard` on mutating routes; never trust client-only gates |
| **Tenancy** | All queries scoped by JWT `workspaceId`; PATCH/ member routes reject id mismatch |
| **Validation** | Zod on server request bodies; client forms use matching Zod + RHF |
| **Data** | Soft-delete filters; set `updated_by` on PATCH |
| **Secrets** | No new secrets; existing `AUTH_SECRET`, `DATABASE_URL` |
| **Tests** | Server unit + e2e (403 matrix); client unit on hooks/schemas; Playwright smoke on settings when routes exist |
| **Ops** | No new deploy services; Render + Neon + Vercel unchanged ([deployment.md](../deployment.md)) |

---

## Performance & scale (v1)

| Topic | Approach |
|-------|----------|
| Workspace profile | Single row per tenant; `GET current` is O(1); safe to cache on client |
| Members list | Paginated API; avoid loading entire directory for large workspaces |
| PATCH | Section-scoped partial updates; no bulk culture rewrites in one giant blob unless spec requires |
| DB | `workspace_id` FK already on members; index on `(workspace_id)` where not already present when adding members queries |
| Future | Analyzer settings and integrations are separate tables/features — not loaded on every settings page |

---

## Paired sprint tasks

| Server (`server/current.yaml`) | Client (`client/current.yaml`) |
|--------------------------------|--------------------------------|
| `task-workspace-read` | `task-workspace-domain` (Phase A + hooks) |
| `task-workspace-patch` | Settings UI (Phase B) — same feature, checklist lines |
| `task-workspace-members` | Members UI (Phase C) — checklist lines |
| `task-workspace-api-tests` | Client unit + e2e rows on checklist |

Do not mark server tasks `done` without API tests; do not mark client workspace work `done` without consuming live APIs (no permanent mock on production routes).

---

## Definition of done (STEP 3)

Matches [workspace.md](../features/workspace.md) acceptance criteria + [sprint-2-progress-checklist.md](./sprint-2-progress-checklist.md): session/hooks, settings on correct subdomain, members + roles with server enforcement, culture/profile on API for downstream JD/AI, tests green in both repos.
