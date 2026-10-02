# Interviews

**Routes:** `(dashboard)/interviews`, `(dashboard)/jobs/[jobId]/interviews`, schedule from candidate profile  
**Domain:** `meetings`  
**Roadmap step:** 9

## Product behavior

**Interview operations** via meeting platform API: list upcoming/past, schedule/reschedule/cancel, show participants and join links, **recording** and transcript metadata for downstream **AI skill analyzer** reports ([ai-skill-analyzers.md](./ai-skill-analyzers.md)). Overview dashboard counts “interviews needing scheduling”.

## Plan

1. **API** — List with filters (recruiter, job, date range); schedule payload (candidate, interviewers, slot, type).
2. **Phase A** — Interviews list page + empty/loading states.
3. **Phase B** — Schedule flow from candidate profile and job tab (shared `ScheduleInterviewDialog`).
4. **Phase C** — Calendar-oriented view if API supports; otherwise enhanced list grouping by day.
5. **Phase D** — Recording/transcript status on past interviews; trigger and display analyzer report status (link to candidate profile).
6. **Phase E** — AI summarize interviewer notes (embedded action) when notes API exists; distinct from full skill analyzer reports.
7. **Dependencies** — Meeting integration layer; analyzer pipeline ([ai-skill-analyzers.md](./ai-skill-analyzers.md)); permissions `interviews.schedule`, `candidates.read`.

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
- [ ] Recording available flag drives analyzer enqueue; report link when complete

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/Interviews.html` | Upcoming and to schedule |
| `designs/screens/ScheduleInterview.html` | Schedule / reschedule flow |
| `designs/screens/InterviewReport.html` | AI interview report on candidate profile |

## References

- [external-integrations.md](./external-integrations.md)
- [candidate-profile.md](./candidate-profile.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
