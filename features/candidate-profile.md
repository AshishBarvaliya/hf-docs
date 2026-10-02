# Candidate profile workspace

**Route:** `(dashboard)/candidates/[candidateId]`  
**Domains:** `candidates`, `assessments`, `meetings`, `emails`, `pipeline`  
**Roadmap step:** 6

## Product behavior

Recruiter **workspace** for one candidate: header with stage and actions (**Advance**, **Reject**, **Message**, **Schedule**), tabs for Summary, Resume, Activity, Assessments, **Analysis** (skill analyzer reports), Emails. AI summary and screening checklist clearly separated from verified candidate data.

**Analysis tab** — Lists **AI skill analyzer** reports per interview (and optional pre-interview profile analysis): weighted scores from job **contribution** matrix, per-skill breakdown, evidence from recordings, assigned analyzer name/version ([ai-skill-analyzers.md](./ai-skill-analyzers.md)).

## Plan

1. **API** — Candidate detail, activity feed, resume URL/embed, assessment summaries, email thread metadata, workflow actions.
2. **Phase A** — Header + Summary tab with real data; workflow actions wired.
3. **Phase B** — Activity timeline; resume viewer (PDF/link).
4. **Phase C** — Assessments tab via assessment integration; Emails tab via email service.
5. **Phase D** — AI summary, **fit vs job requirements**, and **culture alignment** (workspace culture profile + job JD); screening rules display (server-generated, client labels as AI).
6. **Phase E** — **Analysis** tab: analyzer reports, evidence sections, interview cross-links.
7. **Dependencies** — Steps 5, 8–9, 10, 12 (analyzers); permissions for each action.

## Dev

### Contract

| Area | Details |
|------|---------|
| Detail | `GET /api/v1/candidates/:id` — header fields + disposition when API expands beyond list shape |
| Mutations | Advance/reject/message/schedule endpoints per tab; permission-gated |
| Related reads | Activity, assessments, emails, analyzer reports — separate resources when tables exist |
| Permissions | `candidates.read`; separate keys for reject, message, schedule |

### Client

- `candidates/[candidateId]/` layout + tab routes; `useCandidate`, `useCandidateActivity`
- `DomainPanel`, timeline, `AISummary`; reject/advance dialogs with reason codes

### Server

- `server/src/domains/candidates/` — detail + workflow mutations; related domains as they land
- Workspace scope on `candidates.workspace_id`

### Data

Profile reads the same `candidates` row as [candidates-ats.md](./candidates-ats.md). Full column list (migration `0002_foundation_schema_at_scale.sql`):

| Column | Notes |
|--------|--------|
| `id`, `workspace_id` | |
| `name`, `role`, `stage` | header / current list |
| `source` | stored now, API later |
| `resume_url` | resume tab. Stored now, API later |
| `disposition`, `disposition_reason`, `screening_snapshot` | screening outcome for the profile banner. Stored now, API later |
| `created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_at` | row standard; `created_by` / `updated_by` nullable FK → `users.id` |

Activity, assessments, emails, and analyzer reports are separate entities. They are not tables in this migration. AI summary text is produced later; it is not a column on `candidates`.

### Tests

- **Server API:** detail scope, reject/advance with 403 matrix
- **Client unit:** tab permission gates, mutation error handling
- **Client e2e:** open from list; advance/reject with confirmation when API ready

## Acceptance criteria

- [ ] All tabs populated from respective APIs
- [ ] Workflow mutations with error handling
- [ ] AI vs verified visual distinction
- [ ] Fit and culture insights cite job + workspace context (not generic boilerplate)
- [ ] Activity timeline ordered and paginated

## Non-goals

- Client-side resume parsing

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/Profile.html` | Summary layout A (sidebar) |
| `designs/screens/ProfileGrid.html` | Summary layout B (grid variant) |
| `designs/screens/ProfileResume.html` | Resume tab |
| `designs/screens/ProfileNoResume.html` | No resume uploaded |
| `designs/screens/ProfileAssessments.html` | Assessments tab |
| `designs/screens/ProfileEmails.html` | Emails tab |
| `designs/screens/ProfileActivity.html` | Activity timeline |
| `designs/screens/ProfileReject.html` | Reject dialog |
| `designs/screens/ProfileLoading.html` | Loading |
| `designs/screens/ProfileHM.html` | Hiring Manager view |
| `designs/screens/ProfileNoAI.html` | AI unavailable state |
| `designs/screens/Profile1280.html` | Narrow width · Advance menu |
| `designs/screens/EmailCompose.html` | Composer from profile |
| `designs/screens/AssignAssessment.html` | Send assessment modal |
| `designs/screens/InterviewReport.html` | AI interview report on Interviews tab |
| `designs/prototypes/hiring-os-candidate-profile.html` | Interactive profile prototype |

## UI fidelity (canonical mockups)

| Area | Mockup source | Client behavior |
|------|---------------|-----------------|
| Layout | **`Profile.html`** (not `ProfileGrid.html`) | Summary tab: header (stage badge, Advance / Reject / Message / Schedule), left summary + AI blocks, main content |
| Tabs | `Profile.html` | Summary, Resume, Assessments (count), Emails (count), Activity (count) — routes under `[candidateId]/…` |
| Header actions | `Profile1280.html` | Advance overflow menu at narrow widths |
| Reject | `ProfileReject.html` | Modal with reason codes |
| HM / AI | `ProfileHM.html`, `ProfileNoAI.html` | Reduced actions; AI unavailable copy |
| Related flows | `EmailCompose.html`, `AssignAssessment.html`, `InterviewReport.html` | Composer, assign assessment, interview AI report |

**Analysis tab** in product spec ships when analyzer reports API exists; until then mirror mockup placement (Summary sidebar + `InterviewReport.html`).

## References

- [ai-ux.md](../architecture/ai-ux.md)
- [external-integrations.md](./external-integrations.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
