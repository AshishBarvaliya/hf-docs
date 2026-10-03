# Design fidelity — jobs & candidate_applications migrations

**Source:** [design-implementation-fidelity.md](../../architecture/design-implementation-fidelity.md), [jobs.md](../../features/jobs.md), [candidates-ats.md](../../features/candidates-ats.md).

**Status:** partial — `candidate_applications` migration shipped in sprint-ats-foundation; list aggregates and full mockup columns remain.

**Goal:** Database shape matches ATS/jobs mockups before UI claims parity.

## Server

- [x] Migration: `jobs` table (full column list in jobs feature spec including `published_at`, `department`)
- [x] Migration: `candidate_applications` table (per mockup list columns: stage, fit_score, experience_years, source, applied_at, screening fields)
- [x] `GET /api/v1/candidates` returns application-row list shape with pagination
- [ ] `GET /api/v1/jobs` list aggregates for stage distribution, health, low-flow filter
- [x] Unit tests for list query schema
- [ ] API e2e for application list filters and scope
- [ ] `npm run lint`, `npm run test`, `npm run test:e2e` as applicable

## Client

- [ ] Candidates table columns match `CandTable.html` when API ready
- [ ] Jobs table columns match `Jobs.html`
- [x] Unit tests for list response schema

## Docs

- [x] Feature specs + fidelity doc updated (2026-10-03)
- [x] API list conventions + async job route namespace (2026-10-05)
