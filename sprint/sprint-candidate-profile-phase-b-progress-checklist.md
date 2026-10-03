# Sprint candidate profile Phase B — progress checklist

**Sprint id:** `sprint-candidate-profile-phase-b` (**closed** 2026-10-03)  
**Goal:** Resume tab + Activity timeline (STEP 6 Phase B).  
**YAML:** `client/current.yaml` + `server/current.yaml`; archives `client/archive/sprint-candidate-profile-phase-b.yaml`, `server/archive/sprint-candidate-profile-phase-b.yaml`.

| Area | Done | Total | % |
|------|------|-------|---|
| Server | 5 | 5 | 100% |
| Client | 6 | 6 | 100% |
| **Overall** | **11** | **11** | **100%** |

## Server

- [x] `candidate_activity` migration + Drizzle schema (row standard)
- [x] `resumeUrl` on `GET /api/v1/candidates/:candidateId`
- [x] `GET /api/v1/candidates/:candidateId/activity` — paginated, workspace-scoped, `candidates.read`
- [x] Dev seed: activity events + `resume_url` on sample candidate
- [x] Server unit tests (schemas/service) + API e2e (`candidates-profile` / activity)

## Client

- [x] Profile layout with tab routes (Summary, Resume, Activity)
- [x] Resume tab — embed/link PDF or no-resume state
- [x] Activity tab — timeline from `useCandidateActivity`
- [x] `useCandidateActivity` hook + API fetcher + query keys
- [x] Client unit tests (activity schema / helpers)
- [x] Playwright — navigate Resume and Activity tabs from profile

## Docs

- [x] Feature spec Phase B acceptance + **Dev → Data** (`candidate_activity`)
- [x] Domain READMEs (client + server candidates)
- [x] Sprint YAML % updated when tasks complete
