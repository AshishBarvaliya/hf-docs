# Sprint ATS foundation — progress checklist

Paired YAML: `client/current.yaml` + `server/current.yaml` (`sprint-ats-foundation`, **closed**).

| Area | Done | Total | % |
|------|------|-------|---|
| **All** | 13 | 14 | 93% |

## Delivery tracking

- [x] Onboarding sprint archived under `client/archive/` and `server/archive/`
- [x] Active `current.yaml` scoped to sprint-ats-foundation only
- [x] Implementation-task statuses updated for design-fidelity + subdomain tenancy

## Server

- [x] Atomic handoff consumption (`UPDATE … RETURNING`)
- [x] Tenant Origin/JWT guard on authenticated routes
- [x] Public auth + tenant status rate limits
- [x] Migration `candidate_applications` + indexes
- [x] `GET /api/v1/candidates` paginated application-row shape
- [x] Server unit tests (candidates list, handoff)
- [x] Server API e2e (scope; pagination via unit + auth list shape)
- [x] Concurrent handoff replay e2e (`onboarding.e2e-spec.ts`)

## Client

- [x] Nav hides unshipped routes; email hub uses `/emails`
- [x] Candidates client uses paginated list response
- [x] Client unit tests for candidates list parsing

## Docs / gates

- [x] `planning/goals.md` + `planning/decision-gate.md`
- [x] ADR tenant binding + async job route namespace
- [x] `architecture/operational-readiness.md` + hosting phase ADR 007
- [x] Design fidelity IA (`/pipeline`, templates, a11y DoD)

## Deferred (next sprint)

- [ ] Full `CandTable.html` columns and kanban
- [ ] Dedicated candidates list filter e2e + application fixture seed
