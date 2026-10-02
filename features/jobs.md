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

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/jobs/` — `types.ts`, `schemas/`, `api/jobs-api.ts`, `query-keys`, `useJobs`, `useJob`, `useJobMutations` |
| List | `src/app/(dashboard)/jobs/page.tsx` + `JobsTable` in domain |
| Detail shell | `src/app/(dashboard)/jobs/[jobId]/layout.tsx` — header + `Tabs` linking to child routes |
| Child routes | `overview`, `jd`, `candidates`, `pipeline`, … per [folder-structure.md](../architecture/folder-structure.md) |
| UI | `JobCard` for mobile/alternate views; badges for status |

- **URL state:** List filters in searchParams; shareable links for recruiters.
- **Permissions:** `jobs.read`, `jobs.create`, `jobs.edit` on actions and tabs.
- **E2E:** Create draft job (when API ready), open job workspace, switch tabs.

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
