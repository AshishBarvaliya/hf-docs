# Frontend roadmap

**Full product delivery** — we are building the complete Hyreefy client. Steps below are **sequence and dependency order**, not scope reduction. Do not ship “lite” versions unless a spec explicitly lists deferred **non-goals**.

**Data is not sequenced the same way as screens.** The step says when a behavior ships. The table for an entity is designed once, for every later feature that uses it ([persistence.md](../architecture/persistence.md)). A sprint may hide a column from the API. It may not leave the column off the table.

Delivery rules (one feature at a time, no shortcuts, secure prod-ready slices, server built hand in hand): [`../.cursor/rules/product-delivery-principles.mdc`](../.cursor/rules/product-delivery-principles.mdc).

Track execution in [`docs/sprint/client/current.yaml`](../sprint/client/current.yaml) (epic `epic-hyreefy`) and mirror cross-cutting focus in [`docs/sprint/server/current.yaml`](../sprint/server/current.yaml).

```text
STEP 1   Design system + shared components
           ↓
STEP 1.5 Authentication (Auth.js) + Nest JWT guards — **tenant subdomain** + login ([multi-tenancy.md](../architecture/multi-tenancy.md), ADR 004)
           ↓
STEP 1.6 RBAC — roles, permission catalog, guards, `<Can>` (workspace scoped per tenant)
           ↓
STEP 2   App shell + navigation + overview (nav uses RBAC)
           ↓
STEP 3   Company / workspace (settings on current tenant subdomain)
           ↓
STEP 4   Jobs + JD builder
           ↓
STEP 5   Candidates + ATS (table + kanban)
           ↓
STEP 6   Candidate profile workspace
           ↓
STEP 7   Pipeline / workflow builder
           ↓
STEP 8   Assessment platform integration
           ↓
STEP 9   Meeting / interviews integration
           ↓
STEP 10  Emails + automations
           ↓
STEP 11  Analytics & reports
           ↓
STEP 12  AI experiences (embedded) — skill analyzer catalog, assignment, interview reports ([ai-skill-analyzers.md](../features/ai-skill-analyzers.md))
           ↓
STEP 13  Audit log + permission UX polish (RBAC core at 1.6)
```

## Product spine

**Dashboard → Jobs → Candidates → Pipeline → Candidate workspace → Automations → Analytics**

Specialized products (assessment, proctoring, coding, meetings) stay **external**; this client orchestrates them ([ADR 001](../adr/001-orchestration-layer.md)).

## Feature specs

Each step maps to `docs/features/*.md` (Plan + Dev sections). Index: [features/README.md](../features/README.md).

## Repo status vs full product

| Area | Status |
|------|--------|
| Design system | Partial — extend per [design-system-shell.md](../features/design-system-shell.md) |
| Authentication | Sprint 1 foundation — [auth.md](../features/auth.md) + ADR 003 |
| RBAC | Sprint 1 foundation — [rbac.md](../features/rbac.md) |
| Dashboard shell + overview | Shell shipped; overview via `GET /api/v1/analytics/overview` ([overview-dashboard.md](../features/overview-dashboard.md)) |
| Domains | Workspace, jobs (sprint 2 + integrity); candidates list; ATS/profile pending |
| Integrations | Not wired — env layer pending |

Early spike code is documented in [foundation-spike.md](../features/foundation-spike.md); replace with dashboard product routes as each step lands.

When starting work, update `current_focus` in `docs/sprint/client/current.yaml` (and server when API work is in scope) and link the feature doc.
