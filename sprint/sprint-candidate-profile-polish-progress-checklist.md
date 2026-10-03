# Sprint candidate profile polish — progress checklist

**Sprint id:** `sprint-candidate-profile-polish` (**closed** 2026-10-03)  
**Archives:** `client/archive/sprint-candidate-profile-polish.yaml`, `server/archive/sprint-candidate-profile-polish.yaml`  
**Goal:** Activity “load older” cursor UX + accessible profile tab navigation (STEP 6 polish).  
**Scope:** Client infinite activity feed, profile tab `tablist`/`tab` semantics, dev seed rows for pagination smoke, tests.  
**Non-goals:** Assessments/Emails tabs, external integrations (Phase C), pipeline stage definitions (STEP 7), new activity event types, activity sub-filters from mockup.

| Area | Done | Total | % |
|------|------|-------|---|
| Data / DB | 1 | 1 | 100% |
| Server | 4 | 4 | 100% |
| Client | 4 | 4 | 100% |
| Docs | 2 | 2 | 100% |
| **Overall** | **11** | **11** | **100%** |

## Data / DB

- [x] No migration — reuse `candidate_activity` + existing activity list API

## Server

- [x] Document no DB schema change (`task-candidate-profile-polish-no-db`)
- [x] Dev seed — 25+ activity rows for Ada Lovelace (pagination fixtures)
- [x] API e2e — `limit` + `cursor` returns chained pages with `nextCursor`
- [x] Server unit — activity list query schema (`candidate.schemas.spec.ts`)

## Client

- [x] Activity tab — `useInfiniteQuery` + “Load older” when `nextCursor` present
- [x] Profile layout — `role="tablist"` / `role="tab"` + `aria-selected` on tabs
- [x] Client unit — flatten activity pages helper + tab path helpers
- [x] Playwright specs — `candidates-rbac.spec.ts` (run with API + `db:seed`; client `tsc` fixed 2026-10-03)

## Docs

- [x] Feature spec polish slice + acceptance criteria
- [x] Sprint YAML % updated when tasks complete
