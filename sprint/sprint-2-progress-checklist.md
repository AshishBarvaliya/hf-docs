# Sprint 2 — progress checklist

**Sprint id:** `sprint-2` · **Dates:** 2026-10-03 → 2026-10-25  
**YAML % (weighted tasks):** Client **24%** (7/29 weights; STEP 3–4 done; STEP 5+ backlog in YAML) · Server **100%** — [README](./README.md).

**Sprint 2 goal (STEP 3 + STEP 4):** **Complete** as of 2026-10-04. **Sprint closed** — archives: `client/archive/sprint-2.yaml`, `server/archive/sprint-2.yaml`.

**Prior sprint:** [sprint-1-progress-checklist.md](./sprint-1-progress-checklist.md) (closed).  
**Guides:** [sprint-2-development-guide.md](./sprint-2-development-guide.md) · **Tasks:** [workspace-step-3](../planning/implementation-tasks/2026-10-03-workspace-step-3.md), [jobs-step-4](../planning/implementation-tasks/2026-10-04-jobs-step-4.md)

**How to use:** Check `- [x]` when the line is fully done **including tests**.

---

## Sprint 2 focus

| Track | Focus task | Status |
|-------|------------|--------|
| Client | `task-jobs-domain` + `task-jd-builder` (STEP 4) | done |
| Server | `task-jobs-get-api` + mutations + tests (STEP 4 Phase B) | done |
| STEP 3 workspace | client + server | done |

---

## Client checklist

### STEP 3 — Workspace (`feat-roadmap-step-3`)

**Phase A**

- [x] Workspace domain + `WorkspaceProvider` + hooks/fetchers — `task-workspace-domain`

**Phase B — settings UI** (mockups: `designs/screens/Settings*.html`)

- [x] Settings layout + General / Company / Defaults / Culture sections
- [x] Read-only culture branch without `settings.manage` (`SettingsCultureReadOnly.html`)
- [x] `<Can permission="settings.manage">` on edit actions

**Phase C — members**

- [x] Members list + invite (subdomain invite link) + role change UI

**Tests (client)**

- [x] **Unit** — workspace hooks/helpers/schemas
- [x] **E2E** — settings smoke; acme vs beta isolation (`e2e/settings-workspace.spec.ts`)

**Explicitly not sprint 2 focus:** analyzer settings (Phase D), billing/branding upload, invite email worker

### STEP 4 — Jobs (`feat-roadmap-step-4`)

**Phase A**

- [x] Server `jobs` migration + `GET /api/v1/jobs` (filters, pagination, `jobs.read`)
- [x] Server unit + e2e for jobs list and 403
- [x] Client `src/domains/jobs/` types, api, hooks, Zod aligned with server
- [x] `(dashboard)/jobs` list wired to live API (status tabs + search in `searchParams`)
- [x] Job workspace shell — `[jobId]/layout` tab nav

**Phase B — job workspace + JD**

- [x] `GET /jobs/:id`, `POST`/`PATCH`/publish on server with guards
- [x] `useJob`, mutations, query invalidation — `task-jobs-domain`
- [x] Tab routes (overview + JD live; other tabs labeled placeholders)
- [x] `/jobs/new` blank / paste / AI entry + JD editor — `task-jd-builder`
- [x] **Unit** — job + JD Zod schemas, query keys
- [x] **E2E** — jobs list + workspace smoke (`e2e/jobs-workspace.spec.ts`)

**Deferred (full jd-builder.md / jobs.md polish, later sprints):** async AI workers, skills matrix + contribution UI, owner/low-flow list filters, full mockup table columns, workflow template on publish

### Later in sprint file (roadmap order)

- [ ] ATS — STEP 5
- [ ] Profile — STEP 6
- [ ] Workflows — STEP 7
- [ ] Integrations / interviews / analytics — STEP 8–11
- [ ] Automations / AI / audit — STEP 10–13

---

## Server checklist

### Workspace API (`feat-workspace-api`)

- [x] GET `/api/v1/workspaces/current` — `task-workspace-read`
- [x] PATCH workspace settings + `PermissionsGuard` — `task-workspace-patch`
- [x] Members list + invite + role APIs — `task-workspace-members`
- [x] **Tests (API):** unit + e2e for workspace routes and 403 — `task-workspace-api-tests`

### Jobs API (`feat-jobs-api`)

- [x] `jobs` table migration `0003` + JD columns `0004` — `task-jobs-migration`
- [x] GET `/api/v1/jobs` — `task-jobs-list-api`
- [x] **Tests (API):** unit + e2e for jobs list and 403 — `task-jobs-api-tests`
- [x] GET `/api/v1/jobs/:id` — `task-jobs-get-api`
- [x] POST/PATCH/publish — `task-jobs-mutations-api`
- [x] **Tests (API):** unit + e2e for detail and mutations — `task-jobs-detail-api-tests`

---

## Checklist summary

| Section | Done | Total | % |
|---------|------|-------|---|
| Client STEP 3 focus | 7 | 7 | 100% |
| Client STEP 4 | 11 | 11 | 100% |
| Server workspace | 4 | 4 | 100% |
| Server jobs | 6 | 6 | 100% |
| **Sprint 2 goal scope** | **28** | **28** | **100%** |

*YAML client % is lower because STEP 5+ tasks remain in the sprint file as backlog.*
