# Interviews

**Routes:** `(dashboard)/interviews`, `(dashboard)/jobs/[jobId]/interviews`, schedule from candidate profile  
**Domain:** `meetings`  
**Roadmap step:** 9

## Product behavior

**Interview operations** via meeting platform API: list upcoming/past, schedule/reschedule/cancel, show participants and join links, optional recording metadata. Overview dashboard counts “interviews needing scheduling”.

## Plan

1. **API** — List with filters (recruiter, job, date range); schedule payload (candidate, interviewers, slot, type).
2. **Phase A** — Interviews list page + empty/loading states.
3. **Phase B** — Schedule flow from candidate profile and job tab (shared `ScheduleInterviewDialog`).
4. **Phase C** — Calendar-oriented view if API supports; otherwise enhanced list grouping by day.
5. **Phase D** — AI summarize interview notes (embedded action on past interviews) when notes API exists.
6. **Dependencies** — Meeting integration layer; permissions `interviews.schedule`, `candidates.read`.

## Dev

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/meetings/` — full scaffold |
| Routes | `(dashboard)/interviews/page.tsx`, job sub-route |
| UI | Table or list + `ScheduleInterviewDialog`, link-out to meeting product |
| Hooks | `useInterviews`, `useScheduleInterview`, invalidate on success |

- **Timezone:** Display in workspace timezone from `workspace` domain.
- **E2E:** Schedule interview from candidate profile (staging API or mock).

## Acceptance criteria

- [ ] Global and job-scoped interview lists
- [ ] Schedule/reschedule/cancel mutations
- [ ] Join links never fabricated client-side—always from API
- [ ] Overview widget fed by same query definitions

## References

- [external-integrations.md](./external-integrations.md)
- [candidate-profile.md](./candidate-profile.md)
