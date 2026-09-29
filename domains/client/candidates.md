# Candidates domain

## Purpose

Manage and display applicant records in the ATS (list, stages, future detail views).

## Code map

| Area | Path |
|------|------|
| Models / validation | `src/domains/candidates/schemas/candidate.schema.ts` |
| API fetchers | `src/domains/candidates/api/` |
| Query hooks | `src/domains/candidates/hooks/use-candidates.ts` |
| UI | `src/domains/candidates/components/` |
| Public API | `src/domains/candidates/index.ts` |

## Behavior

- `fetchCandidates` validates mock payload with Zod before returning.
- `useCandidates` uses TanStack Query with keys from `candidateKeys`.
- `CandidatesTable` is a client component; routes pass copy via `messages/` (`Candidates` namespace).

## Acceptance criteria

- Home and `/candidates` show the same table with name, role, and stage columns.
- Loading and error states use translated strings.
- E2E: `e2e/home.spec.ts`, `e2e/candidates.spec.ts`.
