# Candidates & ATS

**Routes:** `(dashboard)/candidates`, `(dashboard)/jobs/[jobId]/candidates`  
**Domains:** `candidates`, `pipeline`, `ats`  
**Roadmap step:** 5

## Product behavior

Primary **ATS** surfaces: dense **table** and **kanban** by pipeline stage, filters, bulk actions, scores and experience columns. Stage moves are **workflow-backed**, not client-only. Supports global and job-scoped lists.

**Screening outcomes:** Candidates **auto-rejected** or **auto-filtered** by workflow screening rules show disposition, reason, and criteria snapshot from the server. Recruiters filter by outcome; **override** (advance or un-reject) is permission-gated and audited—not a client-only state change.

## Plan

1. **API** — Candidate list (filters, sort, cursor/page), bulk actions, `POST /workflow/move-stage` (or equivalent).
2. **Phase A** — Production table on real API; retire mock fetcher; URL-synced filters.
3. **Phase B** — Kanban board per pipeline definition; drag calls mutation + optimistic rollback.
4. **Phase C** — Bulk reject/advance/message (permission-gated); export if backend provides.
5. **Phase D** — Auto-reject / auto-filter columns, filters, and detail banner; override flow when `candidates.override_screening` (or equivalent) is granted.
5. **Dependencies** — Pipeline stage definitions from workflow API; design system table; permissions.

**Note:** Existing table on `/` is spike code — re-home to dashboard routes and harden ([foundation-spike.md](./foundation-spike.md)).

## Dev

### Contract

| Area | Details |
|------|---------|
| List | `GET /api/v1/candidates` — workspace-scoped; query: `limit`, `cursor`, `jobId`, `search`, `sort`; response `{ items, limit, nextCursor }` where each item is an application row with `applicationId`, `candidateId`, `name`, `role`, `jobId`, `jobTitle`, `stage`, `fitScore`, `experienceYears`, `source`, `appliedAt` ([api-list-conventions.md](../architecture/api-list-conventions.md)) |
| Stage move | `PATCH /api/v1/candidates/:id/stage` — `:id` is `candidate_applications.id`; body `{ stage }`; returns application list item; requires `candidates.edit`; workspace-scoped 404; workflow rules TBD |
| Permissions | `candidates.read`, `candidates.edit` (exact keys in [permissions.md](../architecture/permissions.md)) |
| Screening | Disposition/reason from server after workflow evaluate ([hiring-workflows.md](./hiring-workflows.md)) |

### Client

- Extend `src/domains/candidates/` — `useCandidates(params)`, filters in query keys; `useUpdateCandidateStage(applicationId)` → PATCH stage (kanban drag)
- `src/domains/pipeline/` — `KanbanBoard`, stage columns; table vs kanban preference
- `CandidatesTable`, `candidate-columns.tsx`; virtualization when >50 rows

### Server

- [`domains/server/candidates/README.md`](../domains/server/candidates/README.md) — list + mutations; [`domains/server/pipeline/README.md`](../domains/server/pipeline/README.md) for stage definitions
- JWT + `workspaceId` + `PermissionsGuard` on every route

### Data

One `candidates` row per person in a workspace. Migration `server/drizzle/0002_foundation_schema_at_scale.sql`. `GET /api/v1/candidates` still returns only `id`, `name`, `role`, `stage`. Lists filter `deleted_at IS NULL`. Columns below marked later are **stored now, API later**.

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `workspace_id` | FK → `workspaces.id` |
| `name`, `role`, `stage` | current list item |
| `source` | where the candidate came from. Later |
| `resume_url` | profile resume tab ([candidate-profile.md](./candidate-profile.md)). Later |
| `disposition` | screening outcome (`rejected`, filtered, or empty). Later |
| `disposition_reason` | reason code, including auto-reject. Later |
| `screening_snapshot` | jsonb criteria snapshot from the server. Later |
| `created_at`, `updated_at` | |
| `created_by`, `updated_by` | nullable FK → `users.id` |
| `deleted_at` | soft delete |

Scores, experience, and per-job applications are not columns on the `candidates` row. They belong on **`candidate_applications`** (one row per candidate–job pipeline). That table is **required** for mockup-faithful ATS UI ([design-implementation-fidelity.md](../architecture/design-implementation-fidelity.md)). Migration ships with jobs + ATS (step 4–5), not on the spike `candidates` list alone.

#### `candidate_applications` (first migration with jobs ATS)

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `workspace_id` | FK → `workspaces.id` |
| `candidate_id` | FK → `candidates.id` |
| `job_id` | FK → `jobs.id` |
| `stage` | Pipeline stage id/slug for this application (kanban column) |
| `fit_score` | nullable numeric 0–100; mockup “Score” + band (Strong/…). Later: tie to analyzer |
| `experience_years` | nullable int; mockup “Experience” column (e.g. `6 yrs`) |
| `source` | e.g. LinkedIn; can duplicate candidate-level source per application |
| `applied_at` | timestamptz; default sort in mockup |
| `disposition`, `disposition_reason`, `screening_snapshot` | per-application screening ([hiring-workflows.md](./hiring-workflows.md)). Stored now, API later |
| `created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_at` | row standard |

`GET /api/v1/candidates` list shape expands to **application rows** (candidate name + job title + columns above). Global list = all applications in workspace; job-scoped route filters `job_id`.

### Tests

- **Server unit + API:** list scope, stage transition, 403 without `candidates.read` (existing e2e baseline); `server/test/candidates-list.e2e-spec.ts` (job/search/cursor + ATS columns); `server/test/candidates-stage.e2e-spec.ts` + `candidates.service.spec.ts` (PATCH stage, 403 without `candidates.edit`)
- **Client unit:** filter/query-key helpers, column defs (`candidate-columns.test.ts`, `fit-score-band.test.ts`)
- **Client e2e:** table load; kanban drag when API ready

## Acceptance criteria

- [ ] Table and kanban parity on stage data
- [x] All stage changes via API (PATCH stage on applications; kanban UI pending)
- [x] Pagination or virtualization for large lists (cursor-paginated `GET /api/v1/candidates`; client list consumes pages)
- [ ] Job-scoped and global routes share domain hooks
- [ ] Auto-reject and auto-filter states visible with server-provided reasons; overrides via API only

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/CandTable.html` | Table view (default) |
| `designs/screens/CandSelected.html` | Bulk selection |
| `designs/screens/CandFiltered.html` | Filters applied |
| `designs/screens/CandTable1280.html` | Table at 1280px |
| `designs/screens/CandKanban.html` | Kanban |
| `designs/screens/CandDrag.html` | Kanban drag in progress |
| `designs/screens/CandKanbanCompact.html` | Single-line kanban variant |
| `designs/screens/CandLoading.html` | Loading |
| `designs/screens/CandNoMatch.html` | No filter matches |
| `designs/screens/CandEmpty.html` | Empty list |
| `designs/prototypes/hiring-os-candidates.html` | Interactive candidates prototype |

## UI fidelity (canonical mockups)

Implement **`CandTable.html`** + **`CandKanban.html`** as default views (table/kanban toggle in toolbar).

| Area | Mockup source | Client behavior |
|------|---------------|-----------------|
| Toolbar | `CandTable.html` | Search “Search in list”; filter chips: Job, Stage, Score, Source, Applied (+ **More** on narrow); default sort **Applied** desc |
| Table columns | `CandTable.html` | Checkbox, Candidate (avatar + name), Job, Stage badge, Score (numeric + segments + band), Experience, Source, Applied, row actions |
| View toggle | `CandTable.html` / `CandKanban.html` | Table \| Kanban; persist preference in UI store |
| Bulk | `CandSelected.html` | Bulk selection bar + actions (permission-gated) |
| Filters | `CandFiltered.html` | Active filter chips reflected in URL query params |
| Kanban | `CandKanban.html`, `CandDrag.html` | Columns = workflow stages; drag → stage API |
| States | `CandLoading`, `CandEmpty`, `CandNoMatch` | Match skeleton, empty, no-results copy |

Deep links from overview use query keys shown in mockup `title` attrs (e.g. `queue=needs-review`, `stage=assessment&queue=decision`).

## References

- [candidate-profile.md](./candidate-profile.md)
- [hiring-workflows.md](./hiring-workflows.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
