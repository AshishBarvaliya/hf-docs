# Candidates domain

**Code:** `src/domains/candidates/`

## Purpose

ATS candidate listing, selection, and profile workspace (Phase A). Central resource for recruiter table/kanban, job-scoped lists, and `/candidates/[candidateId]`.

## Public API

- `CandidatesTable`, `useCandidates`, `useCandidate`, `useCandidateActivity`, `CandidateProfileLayout`, type `Candidate`
- Export via `@/domains/candidates` barrel only from outside the domain

## Data

- Fetchers: `api/candidates-api.ts`
- Query keys: `api/query-keys.ts`
- Validation: `schemas/candidate.schema.ts`

## UI surfaces

| Surface | Status |
|---------|--------|
| Home widget | Done |
| `/candidates` | Done |
| Table view | Done |
| Kanban | Done |
| Profile workspace (Phase A — Summary, Advance/Reject) | Done |
| Profile tabs — Resume, Activity (Phase B) | Done |
| Profile tabs — Assessments, Emails, Analysis | Planned |

## Integration

Stage moves and profile actions must use ATS/workflow API (see [integrations.md](../../architecture/integrations.md)).

## Acceptance (from feature spec)

See [candidates-ats.md](../../features/candidates-ats.md) and [candidate-profile.md](../../features/candidate-profile.md).
