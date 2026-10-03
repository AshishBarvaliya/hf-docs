# Product goals (Hyreefy)

Lightweight outcomes that rank work **before** sprint tasks are created. Update when strategy shifts; link features and sprints to a goal id.

## Q4 2026 — Recruiter OS foundation

| Goal id | Outcome | Success signal |
|---------|---------|----------------|
| `goal-trust` | Tenants cannot see or mutate another workspace’s data | 100% API routes workspace-scoped; Origin/JWT binding tests green |
| `goal-ats-core` | Recruiters manage candidates per job with real pipeline data | `candidate_applications` live; paginated ATS list; stage moves via API (next slice) |
| `goal-fidelity` | Shipped screens match canonical mockups or documented design debt | Route table matches nav; acceptance cites mockup files |
| `goal-scale-ready` | Lists and aggregates stay bounded as data grows | Cursor/offset limits documented; no unbounded workspace reads in hot paths |

## Non-goals (this quarter)

- Full analytics hub (`/analytics`) before mockups exist
- AWS cutover from Render/Neon (see [operational-readiness.md](../architecture/operational-readiness.md))
- Kanban + workflow builder UI before list API + applications stable

## How goals tie to delivery

1. Pick the **highest-impact** goal with **dependencies met** ([roadmap/frontend.md](../roadmap/frontend.md)).
2. Run the [decision gate](./decision-gate.md) on the feature slice.
3. Open a **scoped** sprint in `sprint/client/current.yaml` and `sprint/server/current.yaml` (same `feature_id`).
4. Close the sprint only when feature acceptance, checklist, tests, and YAML agree.
