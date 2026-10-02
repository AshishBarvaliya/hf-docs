# Design fidelity — jobs & candidate_applications migrations

**Source:** [design-implementation-fidelity.md](../../architecture/design-implementation-fidelity.md), [jobs.md](../../features/jobs.md), [candidates-ats.md](../../features/candidates-ats.md).

**Goal:** Database shape matches ATS/jobs mockups before UI claims parity.

## Server

- [ ] Migration: `jobs` table (full column list in jobs feature spec including `published_at`, `department`)
- [ ] Migration: `candidate_applications` table (per mockup list columns: stage, fit_score, experience_years, source, applied_at, screening fields)
- [ ] `GET /api/v1/candidates` returns application-row list shape (or dedicated `GET /applications` with alias)
- [ ] `GET /api/v1/jobs` list aggregates for stage distribution, health, low-flow filter
- [ ] Unit tests for schemas; API e2e for list filters and scope
- [ ] `npm run lint`, `npm run test`, `npm run test:e2e` as applicable

## Client

- [ ] Candidates table columns match `CandTable.html` when API ready
- [ ] Jobs table columns match `Jobs.html`
- [ ] Unit tests for column defs / query keys

## Docs

- [x] Feature specs + fidelity doc updated (2026-10-03)
