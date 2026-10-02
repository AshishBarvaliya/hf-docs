# Workspace STEP 3 (sprint 2)

**Status:** in_sprint  
**Created:** 2026-10-03  
**Feature spec:** [workspace.md](../../features/workspace.md)  
**Dev guide:** [sprint-2-development-guide.md](../../sprint/sprint-2-development-guide.md)

## Summary

Deliver tenant-scoped **workspace settings** and **member management**: server APIs on widened `workspaces` / `workspace_members` columns (`0002`), client domain + settings UI aligned with `designs/screens/Settings*.html`, RBAC via `settings.manage`, and tests on both sides.

## Out of scope (this task)

- Analyzer settings gallery (workspace Phase D → [ai-skill-analyzers.md](../../features/ai-skill-analyzers.md))
- Billing UI, branding asset storage, default pipeline template FK
- Outbound invite email (link-only invite in v1)
- Self-service tenant provisioning
- Roadmap STEP 4+ (jobs, ATS, …)

## Client

- [x] Phase A: `src/domains/workspace/` — types, api, `WorkspaceProvider`, `useWorkspace`
- [x] Phase B: `(dashboard)/settings/*` — General, Company, Defaults, Culture (+ read-only branch)
- [x] Phase C: Members list, invite, role change; subdomain invite links
- [x] `<Can permission="settings.manage">` on edit actions
- [x] **UI unit tests** — hooks, Zod schemas
- [x] **E2E** — settings smoke on tenant host; acme vs beta isolation (`client/e2e/settings-workspace.spec.ts`)

## Server

- [x] `server/src/domains/workspaces/` — module, service, controller
- [x] `GET /api/v1/workspaces/current`
- [x] `PATCH /api/v1/workspaces/:id` — `settings.manage`, id match JWT
- [x] Members GET (paginated), POST invite, PATCH role/status
- [x] **API unit tests** — validation, scope, guard matrix
- [x] **API e2e** — cross-tenant rejection, 403 without `settings.manage`

## Data (server)

- [x] Drizzle + `0002_foundation_schema_at_scale.sql` — workspace profile + member invite columns
- [x] Documented in feature **Dev → Data** and [domains/server/workspaces/README.md](../../domains/server/workspaces/README.md)
- [ ] No further migration unless spec adds columns (e.g. branding URL — currently deferred in spec)

## Contract / API

See **Dev guide** and [domains/server/workspaces/README.md](../../domains/server/workspaces/README.md). Permission key: **`settings.manage`** (not `workspace.settings`).

## Acceptance criteria

- [ ] [workspace.md](../../features/workspace.md) acceptance criteria checked
- [ ] [sprint-2-progress-checklist.md](../../sprint/sprint-2-progress-checklist.md) focus rows checked
- [ ] `npm run lint` + tests pass in `client/` and `server/`

## Open questions

- Session refresh after role change: full re-login vs `update()` session — pick one policy and document in workspace spec when implementing Phase C.

## Promotion to sprint

Tasks live in `docs/sprint/client/current.yaml` (`feat-roadmap-step-3`) and `docs/sprint/server/current.yaml` (`feat-workspace-api`).
