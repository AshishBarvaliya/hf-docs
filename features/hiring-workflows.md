# Hiring workflow builder

**Routes:** `(dashboard)/jobs/[jobId]/settings`, `(dashboard)/automations`, global templates  
**Domains:** `pipeline`, `automations`, `jobs`  
**Roadmap step:** 7

## Product behavior

Visual **hiring process** editor: ordered stages (application → screening → assessment → interviews → offer). Per-stage config (e.g. assessment provider, pass threshold, post-completion branch, notifications). Published workflows drive kanban columns and automation triggers.

## Plan

1. **API** — Workflow CRUD, version/draft semantics, validate-on-publish, stage type registry from backend.
2. **Phase A** — Linear stage list editor; save draft workflow on job.
3. **Phase B** — Stage detail drawer (assessment, interview, email stage types).
4. **Phase C** — Global templates in automations area; clone to job.
5. **Phase D** — Branching/conditional paths if backend supports (UI follows API capability).
6. **Dependencies** — Integration metadata for assessment/meeting/email stage types (steps 8–10).

## Dev

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/pipeline/` — workflow types, `useWorkflow`, `useWorkflowMutations` |
| Automations | `src/domains/automations/` — template library UI |
| Editor | Stage list (sortable), `StageConfigDrawer`, provider pickers |
| Validation | Zod schemas mirroring backend publish rules |

- **No embedded assessment UI** — only configuration and deep links.
- **Sync:** Publishing workflow invalidates pipeline kanban queries for affected jobs.
- **Tests:** Schema tests for stage config; E2E publish workflow on a test job.

## Acceptance criteria

- [ ] Create/edit/publish workflow tied to job or template
- [ ] Stage configs persisted per type
- [ ] Kanban reflects published stages
- [ ] Notifications flags stored and honored by backend

## References

- [integrations.md](../architecture/integrations.md)
- [automations.md](./automations.md)
