# Sprint candidate profile visual fidelity — progress checklist

**Sprint id:** `sprint-candidate-profile-visual-fidelity` (**active**)  
**Paired YAML:** `sprint/client/current.yaml`, `sprint/server/current.yaml`  
**Goal:** Candidate profile workspace matches canonical `Profile.html` (layout A) plus Resume/Activity tab panels and loading/reject states.  
**Scope:** Visual and IA fidelity on existing Phase A/B APIs (detail, activity, resume URL, advance/reject).  
**Non-goals:** `ProfileGrid.html` variant; Assessments, Emails, Analysis tabs; Message/Schedule mutations; AI summary / fit / culture; HM-only layout (`ProfileHM.html`); activity sub-filters from mockup.  
**Dev guide:** [sprint-candidate-profile-visual-fidelity-development-guide.md](./sprint-candidate-profile-visual-fidelity-development-guide.md)

| Area | Done | Total | % |
|------|------|-------|---|
| Data / DB | 0 | 1 | 0% |
| Design | 0 | 1 | 0% |
| Server | 0 | 4 | 0% |
| Client | 0 | 6 | 0% |
| Docs | 2 | 2 | 100% |
| **Overall** | **2** | **14** | **14%** |

## Data / DB

- [ ] No migration — document reuse of `candidates` + `candidate_activity` (`task-profile-fidelity-no-db`)

## Design

- [ ] Mockup audit — `Profile.html`, `ProfileResume.html`, `ProfileActivity.html`, `ProfileLoading.html`, `ProfileReject.html` gaps logged (`task-profile-design-audit`)

## Server

- [ ] Regression on `GET /api/v1/candidates/:candidateId`, activity list, `PATCH` stage, reject (`task-profile-api-regression`)
- [ ] Server unit tests — only if audit exposes contract gaps (`task-profile-server-unit`)
- [ ] API e2e — read, reject, stage, 403 matrix (`task-profile-server-e2e`)
- [ ] `domains/server/candidates/README.md` — fields the profile UI consumes (`task-profile-candidates-readme`)

## Client

- [ ] Profile header — stage badge + action row aligned with mockup (`task-profile-header-layout`)
- [ ] Summary tab — layout A sidebar + main (`task-profile-summary-layout`)
- [ ] Resume + Activity tab panel density (`task-profile-resume-activity-panels`)
- [ ] Loading skeleton + reject dialog states (`task-profile-states-ui`)
- [ ] Client unit tests — tab paths, gates, helpers (`task-profile-client-unit`)
- [ ] Playwright — navigate from list, tabs, advance/reject smoke (`task-profile-playwright`)

## Docs

- [x] Feature spec active slice + **Dev → Tests** rows for this sprint (`features/candidate-profile.md`)
- [x] Sprint README active sprint pointers updated
