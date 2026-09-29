# Automations

**Routes:** `(dashboard)/automations`, job automation tab  
**Domain:** `automations` (+ `pipeline` for workflow linkage)  
**Roadmap step:** 10

## Product behavior

Configure **automation rules** and **workflow templates** at workspace level: triggers (stage entered, assessment completed, time-based if supported), actions (email, move stage, notify recruiter, assign assessment). UI is configuration and monitoring—not execution engine (backend runs automations).

## Plan

1. **API** — Rule CRUD, enable/disable, execution log / last-run status.
2. **Phase A** — Template gallery (clone to job) shared with workflow builder.
3. **Phase B** — Rule builder UI (trigger + conditions + actions).
4. **Phase C** — Execution history and failure alerts on overview dashboard optional widget.
5. **Dependencies** — Workflow stage model; email and assessment integrations for action types.

## Dev

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/automations/` |
| UI | Rule list, `RuleEditor`, link to workflow docs |
| Validation | Zod aligned with backend rule schema |

- **Permissions:** `settings.manage` or dedicated `automations.edit`.
- **Tests:** Schema validation tests; E2E create rule and verify appears on job automation tab.

## Acceptance criteria

- [ ] Workspace automations list with enable toggle
- [ ] Job-level view of attached rules/templates
- [ ] Action types extensible as integrations land
- [ ] No client-side execution of automations

## References

- [hiring-workflows.md](./hiring-workflows.md)
- [emails.md](./emails.md)
