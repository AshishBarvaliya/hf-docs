# Pipeline domain (planned)

**Code:** `src/domains/pipeline/`

## Purpose

Kanban columns, stage transitions, and **hiring workflow** editing. All stage changes and screening outcomes go through workflow/ATS API—never optimistic-only client rules for reject or advance.

## Scope

- Published **rounds** per job (from workspace template or job copy)
- `usePipeline`, kanban board, stage transition mutations
- Workflow editor: round list, type-specific config drawers (assessment, coding, interview, email)
- **Screening criteria** editor: AI-suggested defaults from job JD, user edits, publish validation
- Display screening disposition on candidate cards when API returns `screeningOutcome`

## Hooks (target)

- `usePipeline(jobId | filters)`
- `useWorkflow(jobId | templateId)`, `useWorkflowMutations`
- `useSuggestScreeningCriteria(jobId)` — orchestration-backed

## References

- [candidates-ats.md](../../features/candidates-ats.md)
- [hiring-workflows.md](../../features/hiring-workflows.md)
- [automations.md](../../features/automations.md)

Roadmap **STEP 5–7** (kanban with step 5; builder with step 7).
