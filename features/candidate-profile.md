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

| Piece | Implementation |
|-------|----------------|
| Route | `candidates/[candidateId]/page.tsx` or layout + tab routes |
| Hooks | `useCandidate(id)`, `useCandidateActivity`, mutations for advance/reject |
| UI | `DomainPanel`, timeline in `shared/` or `components/data-display`, `AISummary` |
| Tabs | shadcn Tabs; lazy-load heavy tabs |
| Confirmations | Dialog for reject/advance with reason codes if API requires |

- **Permissions:** Separate gates for reject, message, schedule.
- **E2E:** Open profile from list; advance/reject flows with confirmation.

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

## References

- [ai-ux.md](../architecture/ai-ux.md)
- [external-integrations.md](./external-integrations.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
