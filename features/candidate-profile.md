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

### Active slice (`sprint-candidate-profile`, 2026-10-06 → 2026-10-20)

**Phase A only.** Detail `GET /api/v1/candidates/:candidateId` (header + applications in the workspace), profile route, Summary from `Profile.html`, Advance via existing `PATCH /api/v1/candidates/:applicationId/stage` (`candidates.edit`), Reject with reason (`candidates.reject`, disposition persisted). Checklist: [sprint-candidate-profile-progress-checklist.md](../sprint/sprint-candidate-profile-progress-checklist.md).

### Active slice (`sprint-candidate-profile-phase-b`, 2026-10-06 → 2026-10-20) — closed

**Phase B only.** `resumeUrl` on detail GET; `GET /api/v1/candidates/:candidateId/activity` (paginated); profile **Resume** and **Activity** tabs. Checklist: [sprint-candidate-profile-phase-b-progress-checklist.md](../sprint/sprint-candidate-profile-phase-b-progress-checklist.md).

### Active slice (`sprint-candidate-profile-polish`, 2026-10-03 → 2026-10-17) — closed

**Polish only (STEP 6).** Activity tab **Load older** using existing activity cursor API (`useInfiniteQuery`); profile tab nav `tablist` / `tab` + `aria-selected`; dev seed rows for pagination smoke; Playwright load-more + tab a11y. Checklist: [sprint-candidate-profile-polish-progress-checklist.md](../sprint/sprint-candidate-profile-polish-progress-checklist.md).

### Active slice (`sprint-candidate-profile-visual-fidelity`, 2026-10-03 → 2026-10-17)

**Visual fidelity (STEP 6).** Match canonical `Profile.html` layout A: header (stage + actions), Summary structure, Resume/Activity panel density, `ProfileLoading` / `ProfileReject` states. Reuse Phase A/B APIs; no new tables. Dev guide: [sprint-candidate-profile-visual-fidelity-development-guide.md](../sprint/sprint-candidate-profile-visual-fidelity-development-guide.md). Checklist: [sprint-candidate-profile-visual-fidelity-progress-checklist.md](../sprint/sprint-candidate-profile-visual-fidelity-progress-checklist.md).

**Not this sprint:** Assessments, Emails, Message, Schedule, AI summary / fit / culture, Analysis tab, activity sub-filters from mockup, `ProfileGrid.html`. Phase C still waits on steps 8–12.

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

**`candidate_activity`** (migration `0008_candidate_activity.sql`) — timeline rows for the Activity tab:

| Column | Notes |
|--------|--------|
| `id`, `workspace_id`, `candidate_id` | FK → `candidates.id` |
| `event_type` | `applied`, `stage_changed`, `rejected`, `note` |
| `summary` | Display line |
| `metadata` | jsonb — job title, stages, reason codes, etc. |
| `occurred_at` | When the event happened (ordering) |
| `actor_user_id` | Nullable FK → `users.id` |
| `created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_at` | row standard |

Assessments, emails, and analyzer reports remain separate entities. AI summary text is produced later; it is not a column on `candidates`.

### Tests

- **Server API:** detail scope + `resumeUrl`; activity list scope/pagination; reject/advance with 403 matrix
- **Server unit:** activity query schema; service list activity
- **Client unit:** tab paths, activity schema, permission gates, mutation error handling
- **Client e2e:** open from list; Resume/Activity tab navigation; advance/reject when actions visible; Activity load older when `nextCursor` present; tab `aria-selected`

### Visual fidelity (`sprint-candidate-profile-visual-fidelity`)

- **Server:** regression e2e on detail, activity, stage PATCH, reject; unit only if audit adds contract fields
- **Client unit:** profile layout helpers and action gates touched by mockup alignment
- **Client e2e:** `e2e/candidates-profile.spec.ts` (or extend existing candidates e2e) — list → profile, tab navigation, reject/advance when visible

## Acceptance criteria

### Phase A (shipped)

- [x] `GET /api/v1/candidates/:candidateId` returns workspace header + applications; reject persists disposition (`candidates.reject`)
- [x] Profile route with Summary from API; Advance via `PATCH …/stage`; Reject dialog with reason codes
- [x] Permission gates and mutation error handling on client; server unit + API e2e for detail/reject/403

### Phase B (shipped)

- [x] `resumeUrl` on candidate detail GET
- [x] Paginated activity feed API (`candidates.read`, workspace-scoped)
- [x] Profile Resume tab (PDF embed / no-resume state) and Activity timeline tab
- [x] Server + client tests for Phase B contract

### Polish (`sprint-candidate-profile-polish`)

- [x] Activity tab loads additional pages via **Load older** (cursor)
- [x] Profile tabs expose `role="tablist"` / `role="tab"` and correct `aria-selected`
- [x] Dev seed includes enough Ada Lovelace activity rows for pagination smoke
- [x] Client unit for load older helper; Playwright specs added (verify after client `tsc` green)

### Later phases

- [ ] Remaining tabs populated from respective APIs (Assessments, Emails, Analysis)
- [ ] Workflow mutations (message, schedule) with error handling
- [ ] AI vs verified visual distinction
- [ ] Fit and culture insights cite job + workspace context (not generic boilerplate)

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
