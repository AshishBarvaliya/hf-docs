# Workflow templates, rounds, screening auto-reject

**Status:** proposed  
**Created:** 2026-09-30  
**Feature spec:** [docs/features/hiring-workflows.md](../../features/hiring-workflows.md)  
**Related:** [automations.md](../../features/automations.md), [jd-builder.md](../../features/jd-builder.md), [candidates-ats.md](../../features/candidates-ats.md)

## Summary

Ship workspace and Hyreefy default **workflow templates**, per-job published rounds (resume, assessment, coding, interview, …), **per-round automations** (email, auto meeting), **AI-suggested screening criteria** from JD with admin edit, and **server-side auto-reject / auto-filter** with ATS visibility and permissioned override.

## Out of scope (this task)

- Conditional branching beyond what first API supports
- Custom scripting language for rules
- In-app assessment or meeting UIs (orchestration only)

## Client

- [ ] Template gallery: Hyreefy defaults + workspace list; clone flows
- [ ] Workflow editor: sortable rounds, type picker, stage config drawers
- [ ] Screening criteria editor + AI suggest from JD (`AIAction`)
- [ ] Job settings / JD: attach template, publish workflow
- [ ] ATS: auto-reject/filter badges, filters, override UI
- Paths: `client/src/domains/pipeline/`, `client/src/domains/automations/`
- Tests: Zod schema tests; E2E publish + screening fixture

## Server

- [ ] Workflow template + job workflow schema, publish validation
- [ ] Seed Hyreefy default templates
- [ ] Stage type registry; integration hooks for assessment/coding/meeting/email
- [ ] Screening evaluation service; disposition + audit
- [ ] Emit automation events on stage enter/complete/screening fail
- Paths: `server/src/domains/pipeline/`, automation worker/handlers
- Tests: screening matrix unit tests; 403 on workflow edit; auto-reject integration test

## Contract / API

- Workflow CRUD, publish, screening criteria JSON schema aligned with Zod on client
- Candidate list/detail includes `screeningOutcome`, `rejectionReason`, criteria snapshot id
- AI suggest endpoint: jobId + optional draft workflowId → suggested criteria (non-binding until publish)
- Permissions: `workflows.manage`, move-stage keys, override screening key

## Acceptance criteria

- [ ] Clone default template → edit rounds → publish → kanban matches
- [ ] AI suggest → user edits → publish → candidate below cutoff auto-rejected on apply
- [ ] Interview round with auto-schedule triggers meeting flow server-side
- [ ] Recruiter sees reason; override requires permission and persists in audit

## Open questions

- Single path for screening auto-reject (workflow config only vs duplicate automation rule)—prefer workflow screening block as source of truth, rules for extras
- Soft **auto-filter** vs hard reject: default policy per workspace?

## Promotion to sprint

When starting implementation, align `current_focus` on step 7 (and server mirror), add tasks to sprint YAML, set status to `in_sprint`.
