# Sprint 1 — progress checklist

**Sprint id:** `sprint-1` · **Dates:** 2026-09-28 → 2026-10-11  
**YAML % (final sprint-1):** Client **52%** · Server **100%** — sprint **closed**; active trackers are **sprint-2** ([README](./README.md)).

**Data standard:** [persistence.md](../architecture/persistence.md). `0002_foundation_schema_at_scale.sql` widens the sprint 1 tables.

**How to use:** Check `- [x]` when the line is fully done **including tests**. Checklist % = `checked / total` (count every line below).

**Implementation detail:** [sprint-1-development-guide.md](./sprint-1-development-guide.md)

---

## Sprint 1 focus (auth + RBAC) — closed

**Goal:** Auth.js on tenant subdomain + Nest JWT; RBAC before `(dashboard)` shell; feature Dev sections refreshed. **Remediation (2026-10-03):** [overview](../planning/implementation-tasks/2026-10-03-sprint-1-remediation-overview.md), [auth/RBAC](../planning/implementation-tasks/2026-10-03-sprint-1-remediation-auth-rbac.md).

| Track | Focus task | Status |
|-------|------------|--------|
| Client | `task-feature-dev-server-data` | done (workspace STEP 3 → sprint-2) |
| Server | `task-feature-dev-server-data` | done |

---

## Client checklist

### Engineering / docs (done)

- [x] Cursor DDD rule
- [x] Domain specs under `docs/domains/client/`
- [x] Architecture, ADRs, cursor rules
- [x] Feature specs Plan + Dev
- [x] Sprint YAML + progress rule
- [x] Sprint lifecycle README
- [x] E2E slice policy in rules
- [x] Refresh feature Dev sections (server/data) — `task-feature-dev-server-data`

### STEP 1 — Design system

- [x] StatCard, EmptyState, hiring/ai stubs

### STEP 1.5 — Authentication (`feat-auth-foundation`)

- [x] ADR 003 + auth feature spec
- [x] ADR 004 + multi-tenancy docs
- [x] Auth.js + tenant Host middleware + login — `task-auth-authjs`
- [x] Bearer token in fetchers + 401 handling — `task-auth-api-bearer`
- [x] **Tests (client auth):** unit — tenant helper, `getAccessToken` / session helper
- [x] **Tests (client auth):** unit — login form validation or auth callback wiring (if non-trivial)
- [x] **Tests (client auth):** e2e — login smoke on dev tenant host — `task-auth-client-e2e` (`e2e/auth-login.spec.ts`; requires Nest on `:3001` with seed)

### STEP 1.6 — RBAC (`feat-rbac-foundation`)

- [x] Permission constants + `permissions.md` sync — `task-rbac-catalog`
- [x] `usePermissions` + `<Can>` — `task-rbac-client`
- [x] Pair server JWT permissions + guard — `task-rbac-server`
- [x] **Tests (client RBAC):** unit — `usePermissions`, `<Can>` matrix
- [ ] **Tests (client RBAC):** e2e — optional 403/no-access smoke when harness exists *(deferred; not required for sprint-1 close)*

### STEP 2 — Shell + overview (`feat-roadmap-step-2`)

- [x] Dashboard layout — sidebar, header, auth-protected `(dashboard)` group — `task-dashboard-layout`
- [x] **Tests (client shell):** unit — permission-filtered nav (`nav-config.test.ts`)
- [x] Overview page + redirect from `/` — `task-overview-page`
- [x] **Tests (client overview):** unit — API map + permission filters (`overview-metrics.test.ts`)
- [x] **Remediation:** overview live API + auth/RBAC e2e (`e2e/candidates-rbac.spec.ts`, `server/test/analytics.e2e-spec.ts`)

### Later in sprint file (not focus yet)
- [ ] Workspace domain — STEP 3
- [ ] Jobs + JD — STEP 4
- [ ] ATS — STEP 5
- [ ] Profile — STEP 6
- [ ] Workflows — STEP 7
- [ ] Integrations / interviews / analytics — STEP 8–11
- [ ] Automations / AI / audit — STEP 10–13

---

## Server checklist

### Docs

- [x] E2E slice docs in sprint README
- [x] Feature Dev server/data rows — `task-feature-dev-server-data`

### Candidates API (spike)

- [x] Nest + Drizzle scaffold
- [x] GET `/api/v1/candidates` + seed
- [x] Client wired to API
- [x] **Tests (API):** e2e 401/403 — `server/test/auth.e2e-spec.ts` (this line was still open after the auth and RBAC suites landed)

### STEP 1.5 — Auth API (`feat-auth-api`)

- [x] `tenants` + `workspaces` schema + seed — `task-tenant-schema`
- [x] Tenant-scoped login + JWT guard on candidates — `task-auth-jwt-guard`
- [x] Dev user seed + Auth.js contract — `task-auth-dev-user`
- [x] **Tests (API auth):** unit — login rejects wrong tenant; JWT strategy/guard
- [x] **Tests (API auth):** e2e — `POST /auth/login`, `GET /candidates` 401 without token

### STEP 1.6 — RBAC API (`feat-rbac-api`)

- [x] RBAC schema + seed — `task-rbac-schema`
- [x] `PermissionsGuard` on candidates — `task-rbac-guard`
- [x] JWT `permissions[]` claims — `task-rbac-jwt-claims`
- [x] **Tests (API RBAC):** unit — role → permission matrix
- [x] **Tests (API RBAC):** e2e — `GET /candidates` 403 without `candidates.read`
- [x] Widen foundation tables for later features plus `created_by`, `updated_by`, `deleted_at` — `task-foundation-schema-at-scale`

---

## Feature specs (acceptance)

### [auth.md](../features/auth.md)

- [x] Known tenant subdomain; unknown → tenant-not-found
- [x] Unauthenticated routes redirect to login
- [x] Login establishes tenant + workspace; scoped candidates (Auth.js session + `auth.e2e-spec.ts`; Playwright smoke is still the open client line above)
- [x] No cross-tenant login without membership
- [x] Nest 401 without bearer
- [x] No secrets in client bundle
- [x] RBAC complete before dashboard

### [rbac.md](../features/rbac.md)

- [x] Permission keys match server enforcement
- [x] JWT/session includes tenant, workspace, permissions
- [x] Cross-tenant isolation tested
- [x] `GET /candidates` requires `candidates.read`
- [x] `<Can>` / `usePermissions` before dashboard
- [x] Two seeded roles provable in tests

---

## Checklist summary

Update counts when you edit this file:

| Section | Done | Total | % |
|---------|------|-------|---|
| Client (all lines above) | 24 | 32 | 75% |
| Server (all lines above) | 17 | 17 | 100% |
| Feature acceptance (auth + rbac) | 13 | 13 | 100% |
| **Rough combined** | **54** | **62** | **87%** |

*Recount “Done / Total” when adding or checking items.*
