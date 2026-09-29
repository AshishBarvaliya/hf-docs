# Job creation, JD paste/AI, workspace culture

**Status:** proposed  
**Created:** 2026-09-30  
**Feature spec:** [docs/features/jd-builder.md](../../features/jd-builder.md)  
**Related:** [workspace.md](../../features/workspace.md), [jobs.md](../../features/jobs.md)

## Summary

Implement `/jobs/new` with paste-JD parse, AI JD generate, and blank paths; posting fields (location, compensation, about company, expiry); free-text skills/requirements with extract/normalize; workspace **culture & company profile** for defaults and downstream candidate AI analysis.

## Out of scope (this task)

- External job board syndication
- Compensation benchmarking APIs

## Client

- [ ] Wizard entry: paste / AI / blank
- [ ] `PasteJdStep`, parse loading + review diff
- [ ] Posting sections + workspace default for about company
- [ ] Skills/requirements section (text + chips)
- [ ] Settings: culture profile form (if not done in workspace step 3)
- Paths: `client/src/domains/jobs/`, `client/src/domains/workspace/`
- Tests: jd.schema tests; E2E paste → publish

## Server

- [ ] Job draft schema with posting + requirements fields
- [ ] `parse-jd`, `generate-jd` orchestration endpoints
- [ ] Workspace company + culture profile on workspace entity
- [ ] Publish validation (expiry, required fields)
- Paths: `server/src/domains/jobs/`, workspace module
- Tests: parse fixtures; culture snapshot included in AI analysis payload

## Contract / API

- Job draft JSON aligned with client Zod
- Workspace `GET/PATCH` includes `companyProfile`, `cultureProfile`
- AI analysis requests include `jobId`, structured requirements, culture snapshot id

## Acceptance criteria

- [ ] Three creation paths produce same draft shape
- [ ] About company inherits workspace; job override persists
- [ ] Posting expiry enforced on publish/list
- [ ] Candidate profile AI receives job + culture context server-side

## Promotion to sprint

Align with roadmap step 3 (workspace profile) + step 4 (jobs/JD); set `in_sprint` when building.
