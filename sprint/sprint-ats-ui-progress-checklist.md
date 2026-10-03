# Sprint ATS UI — progress checklist

Paired YAML: `client/current.yaml` + `server/current.yaml` (`sprint-ats-ui`, **active**).

| Area | Done | Total | % |
|------|------|-------|---|
| **All** | 11 | 11 | 100% |

## Server

- [x] Dev seed — `candidate_applications` fixtures linked to jobs
- [x] `GET /api/v1/candidates` filter e2e (job, search, cursor)
- [x] PATCH application stage endpoint + `candidates.edit` guard
- [x] Server unit tests for stage transition
- [x] Server API e2e for stage transition + 403

## Client

- [x] CandTable columns from paginated application rows
- [x] Table / kanban toggle + kanban columns
- [x] Filter chips + URL query params
- [x] Client unit tests (columns, view toggle)
- [x] Playwright candidates table smoke

## Docs

- [x] `candidates-ats.md` acceptance updated as slices land
