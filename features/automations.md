# Automations

**Routes:** `(dashboard)/automations`, job **Automation** tab  
**Domain:** `automations` (+ `pipeline` for workflow linkage)  
**Roadmap step:** 10

## Product behavior

Configure **automation rules** and **workflow templates** at workspace level. The **execution engine runs on the server**; the client is configuration, preview, and monitoring.

### Two layers (same mental model for admins)

| Layer | What admins configure |
|-------|------------------------|
| **Workflow template / job workflow** | Ordered rounds plus **built-in per-round actions** (email on enter, auto-schedule interview, assign assessment, move/reject on score). Primary UX in [hiring-workflows.md](./hiring-workflows.md). |
| **Automation rules** | Optional workspace or job rules: **trigger** (stage entered, stage completed, assessment completed, screening failed, time-based if supported) + **conditions** + **actions** (email, move stage, notify recruiter, assign assessment, reject with reason). |

Hyreefy **default workflow templates** appear in the automations/template gallery; cloning copies rounds **and** default automation hooks into the workspace.

### Triggers tied to hiring rounds

Examples recruiters expect (all server-enforced):

- **Stage entered** — Send “next steps” email; for interview rounds, start **auto meeting** flow (booking link or scheduled slot per integration).
- **Assessment / coding completed** — If score ≥ cutoff → advance; else → reject or alternate branch.
- **Screening evaluated** — **Auto-reject** or **auto-filter** per [hiring-workflows.md](./hiring-workflows.md) criteria; optional rejection email.
- **Time-based** — Reminders (e.g. assessment not started in 48h) when backend scheduler supports.

### Monitoring

Rule list with enable/disable; **execution log** / last-run status; failures surfaced on overview dashboard optional widget.

## Plan

1. **API** — Rule CRUD, enable/disable, execution log; idempotent handlers for stage events from pipeline domain.
2. **Phase A** — Template gallery (Hyreefy defaults + workspace) shared with workflow builder; clone to job.
3. **Phase B** — Rule builder UI (trigger + conditions + actions); align triggers with workflow round events.
4. **Phase C** — Execution history and failure alerts; link from candidate profile (“automation: rejected at screening”).
5. **Dependencies** — Published workflow and screening evaluation (step 7); email, assessment, meeting action types (steps 8–10).

## Dev

### Contract

| Area | Details |
|------|---------|
| Rules | `GET/POST/PATCH /api/v1/automations` — workspace rules; enable toggle |
| Templates | Hyreefy defaults + workspace clones |
| Execution | Server-only on workflow stage transitions; no client triggers |
| Permissions | `automations.edit`, `workflows.manage` (catalog) |

### Data

`automation_rules` jsonb definition + workspace FK; execution log rows or reuse audit pipeline when step 10 ships.

### Server

- `server/src/domains/automations/` — rule CRUD, dispatcher hooked from pipeline domain

### Client

- `src/domains/automations/` — `RuleEditor`, template gallery; Zod mirrors server schema

### Tests

- **Server unit:** rule schema, screening auto-reject side effects
- **Client unit:** Zod validation tests
- **Client e2e:** create rule → fixture candidate state when workflow API ready

## Acceptance criteria

- [ ] Workspace automations list with enable toggle
- [ ] Template gallery includes Hyreefy defaults and workspace templates
- [ ] Job-level view of attached rules and workflow-derived automations
- [ ] Action types extensible as integrations land
- [ ] Auto email and auto meeting triggers configurable and executed server-side only
- [ ] Screening auto-reject / auto-filter fires from rules or workflow screening config (single authoritative path documented in server pipeline domain)
- [ ] No client-side execution of automations

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/AutomationRules.html` | Workspace automation rules |
| `designs/screens/AutomationRuleEdit.html` | Rule builder (assessment reminder) |
| `designs/screens/JobAutomation.html` | Per-job automation tab |

## References

- [hiring-workflows.md](./hiring-workflows.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
- [emails.md](./emails.md)
- [interviews.md](./interviews.md)
