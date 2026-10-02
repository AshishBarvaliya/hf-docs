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
| List | `GET /api/v1/candidates` — workspace-scoped; filters: job, stage, search, sort; pagination |
| Stage move | `PATCH /api/v1/candidates/:id/stage` (or pipeline API) — server validates workflow |
| Permissions | `candidates.read`, `candidates.edit` (exact keys in [permissions.md](../architecture/permissions.md)) |
| Screening | Disposition/reason from server after workflow evaluate ([hiring-workflows.md](./hiring-workflows.md)) |

### Client

- Extend `src/domains/candidates/` — `useCandidates(params)`, filters in query keys
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

Scores, experience, and per-job applications are not columns on this row. They belong on application and analyzer-report tables, which are new entities and ship with jobs / ATS — not in this migration. `name` / `role` / `stage` remains the list contract until that slice.

### Tests

- **Server unit + API:** list scope, stage transition, 403 without `candidates.read` (existing e2e baseline)
- **Client unit:** filter/query-key helpers, column defs
- **Client e2e:** table load; kanban drag when API ready

## Acceptance criteria

- [ ] Table and kanban parity on stage data
- [ ] All stage changes via API
- [ ] Pagination or virtualization for large lists
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

## References

- [candidate-profile.md](./candidate-profile.md)
- [hiring-workflows.md](./hiring-workflows.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
