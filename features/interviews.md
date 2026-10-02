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

### Contract

| Area | Details |
|------|---------|
| List | `GET /api/v1/interviews` — global and `?jobId=` scoped |
| Schedule | `POST /api/v1/interviews` — reschedule/cancel variants; join URL in response only |
| Recording | Webhook from meeting product → analyzer enqueue ([ai-skill-analyzers.md](./ai-skill-analyzers.md)) |
| Permissions | `interviews.schedule`, `candidates.read` |

### Data

`interviews` table (first migration with meetings slice): `workspace_id`, `candidate_id`, `job_id`, `scheduled_at`, `status`, `external_meeting_id`, `join_url`, row standard columns.

### Server

- `server/src/domains/meetings/` — proxy to meeting integration; workspace scope

### Client

- `src/domains/meetings/` — `(dashboard)/interviews`, `ScheduleInterviewDialog`
- Timezone from `useWorkspace()`

### Tests

- **Server API:** schedule/cancel authz; join URL never empty without API
- **Client unit:** list filters, dialog validation
- **Client e2e:** schedule from candidate profile when API ready

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
