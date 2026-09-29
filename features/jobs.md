# Jobs

**Routes:** `(dashboard)/jobs`, `(dashboard)/jobs/[jobId]/…`  
**Domain:** `jobs`  
**Roadmap step:** 4

## Product behavior

Full **job lifecycle** management: list by status (draft, active, paused, closed), job workspace with tabs (overview, JD, candidates, pipeline, assessments, interviews, emails, automation, analytics, settings). Table columns: job title, candidate count, aggregate stage, owner, status.

## Plan

1. **API** — Jobs CRUD, list filters, job header aggregate (counts, health flags).
2. **Phase A** — `jobs` domain scaffold + list + detail **layout shell** with tab routes (tabs can be placeholders initially but routes must exist).
3. **Phase B** — Status filters, pagination, search; owner assignment.
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

## References

- [jd-builder.md](./jd-builder.md)
