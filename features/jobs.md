# Jobs

**Routes:** `(dashboard)/jobs`, `(dashboard)/jobs/[jobId]/…`  
**Domain:** `jobs`  
**Roadmap step:** 4

## Product behavior

Full **job lifecycle** management: list by status (draft, active, paused, closed), job workspace with tabs (overview, JD, candidates, pipeline, assessments, interviews, emails, automation, analytics, settings). Table columns: job title, candidate count, aggregate stage, owner, status, **posting expiry** when set.

### Creating a job

Primary entry: **`/jobs/new`** — [jd-builder.md](./jd-builder.md) wizard:

- Paste external JD, **generate with AI**, or start blank
- Fill posting fields (location, compensation, about company, expiry, …)
- Skills/requirements via short text and/or extracted tags
- Optional workflow template before **publish**

Published jobs appear in the list; drafts remain editable. Expired postings show distinct status or auto-transition per backend rules.

## Plan

1. **API** — Jobs CRUD, list filters, job header aggregate (counts, health flags).
2. **Phase A** — `jobs` domain scaffold + list + detail **layout shell** with tab routes (tabs can be placeholders initially but routes must exist).
3. **Phase B** — `jobs/new` creation route wired to JD builder entry; status filters, pagination, search; owner assignment.
4. **Phase C** — Job overview tab (health, low flow warnings linking to analytics).
5. **Dependencies** — Design system tables; workspace for owners; candidates/pipeline tabs light up in steps 5–7.

## Dev

### Contract

| Area | Details |
|------|---------|
| List | `GET /api/v1/jobs` — filters: status, search, owner; pagination |
| CRUD | `GET/PATCH/POST /api/v1/jobs`, `POST /api/v1/jobs/:id/publish` |
| Header aggregate | Counts, health flags on `GET /api/v1/jobs/:id` |
| Permissions | `jobs.read`, `jobs.create`, `jobs.edit` on routes and UI actions |

### Data

First migration for `jobs` (full row standard per [persistence.md](../architecture/persistence.md)):

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `workspace_id` | FK → `workspaces.id` |
| `title`, `status` | `draft`, `active`, `paused`, `closed` |
| `owner_id` | nullable FK → `users.id` |
| `posting_expires_at` | nullable |
| `workflow_template_id` | nullable FK when workflows table exists — stored now, API later |
| `created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_at` | row standard |

JD body, skills matrix, and posting fields may live on `jobs` jsonb columns or child tables — name in migration when JD slice ships ([jd-builder.md](./jd-builder.md)).

### Server

- `server/src/domains/jobs/` (planned) — CRUD, publish, list filters; Zod DTOs; JWT + workspace scope + `PermissionsGuard`

### Client

- `src/domains/jobs/` — types, schemas, `api/jobs-api.ts`, `useJobs`, `useJob`, `useJobMutations`
- `(dashboard)/jobs/page.tsx`, `[jobId]/layout.tsx` + tab child routes per [folder-structure.md](../architecture/folder-structure.md)
- List filters in `searchParams`; `JobCard` / badges for status

### Tests

- **Server unit + API:** CRUD, publish transition, 403 without `jobs.read`
- **Client unit:** Zod schemas, query key helpers
- **Client e2e:** Create draft, open workspace, switch tabs when API ready

## Acceptance criteria

- [ ] Full list filters and paginated table
- [ ] Job workspace with all tab routes wired
- [ ] Mutations invalidate `useJobs` / `useJob` query keys
- [ ] i18n for statuses and empty states
- [ ] Create draft from `/jobs/new` (paste, AI, or blank) per jd-builder spec

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/Jobs.html` | Jobs table |
| `designs/screens/JobsCards.html` | Cards layout variant |
| `designs/screens/JobsLoading.html` | Loading |
| `designs/screens/JobsEmpty.html` | Empty workspace |
| `designs/screens/JobsNoPaused.html` | No paused jobs |
| `designs/screens/JobsViewer.html` | List without edit rights |
| `designs/screens/JobWorkspace.html` | Job workspace overview tab |
| `designs/screens/JobWorkspaceAI.html` | Job-level explain drop-off |
| `designs/screens/JobDraft.html` | Draft job setup |
| `designs/screens/JobViewer.html` | Workspace read-only |
| `designs/screens/JobTabsOverflow.html` | Tab bar at 1280px |
| `designs/screens/JobAutomation.html` | Automation tab ([automations.md](./automations.md)) |
| `designs/prototypes/hiring-os-jobs.html` | Interactive jobs prototype |

JD creation flow: [jd-builder.md](./jd-builder.md) mockups (`Main.html`, etc.).

## References

- [jd-builder.md](./jd-builder.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
