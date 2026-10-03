# Sprint candidate profile visual fidelity — development guide

**Sprint id:** `sprint-candidate-profile-visual-fidelity`  
**Feature spec:** [candidate-profile.md](../features/candidate-profile.md) (Phase A/B behavior already shipped)  
**Design canon:** `designs/screens/Profile.html`, `ProfileResume.html`, `ProfileActivity.html`, `ProfileLoading.html`, `ProfileReject.html` ([design-implementation-fidelity.md](../architecture/design-implementation-fidelity.md))

## Goal

Recruiter **candidate profile** matches mockup **layout A** on real APIs: header with stage and primary actions, Summary structure, Resume and Activity tab panels, and honest loading/reject UI. Server keeps the Phase A/B contract; extend APIs only if the audit proves a field is missing.

## Scope

### In scope

| Area | Deliverable |
|------|-------------|
| **Design** | Audit vs `Profile.html`; implement header, Summary layout, tab chrome, loading/reject states |
| **Server** | No new tables; regression tests on detail, activity pagination, stage PATCH, reject |
| **Client** | `(dashboard)/candidates/[candidateId]/*` layout and tab routes in `domains/candidates/` |
| **Tests** | Server e2e matrix; client unit for helpers/gates; Playwright profile smoke |

### Non-goals

- `ProfileGrid.html` (variant B)
- Assessments, Emails, Analysis tab content (steps 8–12)
- Message / Schedule workflow APIs
- AI summary, fit, culture blocks (`ProfileNoAI` defer until Phase D)
- Role-specific HM layout (`ProfileHM.html`) unless trivial CSS reuse

## Data / DB

**No migration.** Use `candidates` + `candidate_activity` from Phase B (`0008_candidate_activity.sql`). Task: `task-profile-fidelity-no-db`.

## API contract (unchanged baseline)

| Method | Path | Permission |
|--------|------|------------|
| GET | `/api/v1/candidates/:candidateId` | `candidates.read` |
| GET | `/api/v1/candidates/:candidateId/activity` | `candidates.read` |
| PATCH | `/api/v1/candidates/:applicationId/stage` | `candidates.edit` |
| POST | reject endpoint per [candidate-profile.md](../features/candidate-profile.md) | `candidates.reject` |

If the design audit requires a **new response field**, update Zod + feature **Dev → Contract** in the same sprint before UI depends on it.

## Client touchpoints

- `src/app/(dashboard)/candidates/[candidateId]/` — layout + tab pages
- `src/domains/candidates/components/candidate-profile-*.tsx`
- Hooks: `useCandidate`, `useCandidateActivity`, stage/reject mutations

## Test commands

```bash
# server/
npm run lint && npm run test && npm run test:e2e

# client/
npm run lint && npm run test:unit && npm run test:e2e
```

## Paired tasks

| Client | Server |
|--------|--------|
| `task-profile-design-audit` | `task-profile-fidelity-no-db` |
| `task-profile-header-layout` … `task-profile-playwright` | `task-profile-api-regression` … `task-profile-server-e2e` |

Keep `current_focus.task_id` aligned on the same slice when working cross-repo (audit + no-db first).
