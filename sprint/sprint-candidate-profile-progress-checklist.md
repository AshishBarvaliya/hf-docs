# Sprint candidate profile — progress checklist

Paired YAML: `client/current.yaml` + `server/current.yaml` (archived copy: `client/archive/sprint-candidate-profile.yaml`, `server/archive/sprint-candidate-profile.yaml`). `sprint-candidate-profile`, **closed** 2026-10-03.

Scope is [candidate-profile.md](../features/candidate-profile.md) **Phase A** only: header + Summary (`Profile.html`), Advance via existing stage PATCH, Reject with reason. Activity, resume viewer, assessments, emails, AI summary, and Analysis are later phases.

| Area | Done | Total | % |
|------|------|-------|---|
| **All** | 11 | 11 | 100% |

## Server

- [x] `GET /api/v1/candidates/:candidateId` — workspace-scoped header plus applications (`candidates.read`; other workspace → 404)
- [x] Reject mutation with reason code; `candidates.reject`; persist disposition + reason
- [x] Server unit tests for detail and reject
- [x] Server API e2e for detail scope, reject success, and 403 without `candidates.reject`

## Client

- [x] Route `(dashboard)/candidates/[candidateId]` opened from the ATS table
- [x] Header + Summary tab from the detail API (`Profile.html` layout A)
- [x] Advance calls existing application stage PATCH; Reject dialog (`ProfileReject.html`) with `<Can permission="candidates.reject">` and error handling
- [x] Client unit tests (permission gates, mutation errors)
- [x] Playwright: open profile from the list; advance/reject confirmation

## Docs

- [x] `candidate-profile.md` Phase A acceptance updated as the slice lands

## Deferred (not this sprint)

- Message and Schedule header actions (emails + interviews)
- Resume, Activity, Assessments, Emails, Analysis tabs
- AI summary, fit, and culture blocks
