# JD builder

**Routes:** `(dashboard)/jobs/new`, `(dashboard)/jobs/[jobId]/jd`  
**Domain:** `jobs` (+ workflow template picker → `pipeline` / `automations`)  
**Roadmap step:** 4

## Product behavior

Multi-step **job description authoring**: basics, rich-text description, structured requirements (skills, experience), hiring process stages. **Save draft** and **Publish**. Inline AI actions on description and requirements (not a separate AI page).

## Plan

1. **API** — Job draft payload, publish transition, attachment to workflow template ID.
2. **Phase A** — Form sections with Zod; persist draft; rich text editor choice (accessible, i18n-friendly).
3. **Phase B** — Skill tags (combobox), employment/location enums synced with backend.
4. **Phase C** — AI actions via orchestration API (streaming optional later); all AI output marked suggested.
5. **Phase D** — Hiring process section binds to workflow builder ([hiring-workflows.md](./hiring-workflows.md)).
6. **Dependencies** — Jobs domain; AI UX components; workflow list API.

## Dev

| Piece | Implementation |
|-------|----------------|
| Components | `domains/jobs/components/jd-builder/` — section components |
| Form | `useZodForm` + `jobs/schemas/jd.schema.ts`; single form or stepped wizard with shared schema |
| Mutations | `useSaveJobDraft`, `usePublishJob` with toast + error surfaces |
| AI | `components/ai/AIAction` calling `jobs/api/ai-jd-api.ts` |
| Route | Thin page composing `JdBuilder` |

- **State:** Dirty form warning on navigate away; autosave draft debounced if API supports.
- **Permissions:** `jobs.create` / `jobs.edit`.
- **Tests:** Zod schema unit tests; E2E happy path draft → publish.

## Acceptance criteria

- [ ] All sections validated and persisted
- [ ] Draft vs published UI states
- [ ] AI actions with distinct styling and errors handled
- [ ] Workflow template linked on publish

## Non-goals

- Running assessments from this screen

## References

- [ai-ux.md](../architecture/ai-ux.md)
