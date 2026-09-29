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

| Piece | Implementation |
|-------|----------------|
| Domain | Extend `src/domains/candidates/` — real API, filters type, `useCandidates(params)` |
| Pipeline | `src/domains/pipeline/` — `usePipeline`, `KanbanBoard`, stage column components |
| Table | `CandidatesTable`, `candidate-columns.tsx`; virtualization when >50 rows |
| View toggle | Zustand or user preference: table vs kanban |
| Shared types | `ats/types.ts` for `PipelineStage`, stage labels |

- **Query keys:** Include jobId, stage, search, sort in `query-keys.ts`.
- **Mutations:** Invalidate list + kanban + candidate detail on stage change.
- **E2E:** Table load; kanban drag (mock server or staging API).

## Acceptance criteria

- [ ] Table and kanban parity on stage data
- [ ] All stage changes via API
- [ ] Pagination or virtualization for large lists
- [ ] Job-scoped and global routes share domain hooks
- [ ] Auto-reject and auto-filter states visible with server-provided reasons; overrides via API only

## References

- [candidate-profile.md](./candidate-profile.md)
- [hiring-workflows.md](./hiring-workflows.md)
