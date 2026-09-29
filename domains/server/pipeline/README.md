# Pipeline / workflow domain (planned)

**Code:** `server/src/domains/pipeline/` (or equivalent module name)

## Purpose

**Authoritative** hiring workflows: templates, job-bound published versions, stage transitions, screening evaluation, and events consumed by the automations runner.

## Responsibilities

| Area | Behavior |
|------|----------|
| **Templates** | Hyreefy default templates (read-only seed); workspace template CRUD; draft/publish/version |
| **Job workflow** | Attach template or fork; publish drives pipeline stage definitions for that job |
| **Stage registry** | Typed rounds: application, resume screening, assessment, coding, interview, offer, … |
| **Screening** | Evaluate candidate vs published criteria/cutoffs on apply or stage entry; emit `auto_reject`, `auto_filter`, or pass |
| **Transitions** | `move-stage`, pass/fail from integration webhooks; reject with reason codes |
| **Events** | Publish domain events for automations (email, schedule meeting, assign assessment) |

## API (illustrative)

Document concrete routes when implemented; expected capabilities:

- `GET/POST/PATCH` workspace workflow templates
- `GET/POST/PATCH` job workflow; `POST …/publish`
- `POST …/candidates/:id/move-stage`
- `POST …/screening/evaluate` (internal or on apply pipeline)
- Webhooks from assessment/meeting products → stage completion handlers

## Permissions

- Template and workflow edit: `workflows.manage` (or catalog equivalent)
- Stage move: recruiter permissions + workflow rules
- Override auto-reject: dedicated key; audit log entry

## References

- [hiring-workflows.md](../../features/hiring-workflows.md)
- [automations.md](../../features/automations.md)
- [candidates-ats.md](../../features/candidates-ats.md)
- ADR [001-orchestration-layer.md](../../adr/001-orchestration-layer.md)
