# Candidates domain

**Code:** `src/domains/candidates/`

## Purpose

ATS candidate listing, selection, and (future) profile workspace data. Central resource for recruiter table/kanban and job-scoped candidate views.

## Public API

- `CandidatesTable`, `useCandidates`, type `Candidate`
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
| Kanban | Planned |
| Profile workspace | Planned |

## Integration

Stage moves and profile actions must use ATS/workflow API (see [integrations.md](../../architecture/integrations.md)).

## Acceptance (from feature spec)

See [candidates-ats.md](../../features/candidates-ats.md) and [candidate-profile.md](../../features/candidate-profile.md).
