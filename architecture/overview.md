# Hiring OS — client architecture overview

The client app is the **Hiring OS**: an orchestration layer for recruiting. It is **not** the assessment engine, proctoring stack, or meeting product. Those are separate backends/products; this app coordinates them through APIs and presents a unified recruiter experience.

**Multi-tenant:** Each customer uses a dedicated **subdomain**; tenant context comes from the request host before auth and RBAC. See [multi-tenancy.md](./multi-tenancy.md) and [ADR 004](../adr/004-subdomain-multi-tenancy.md).

## Product boundary

| Owns (client) | Does not own (external products) |
|---------------|----------------------------------|
| Dashboard, jobs, JD builder UI | Coding assessment runtime |
| ATS views (table, kanban, profile) | Proctoring |
| Pipeline and workflow configuration | Live meeting rooms |
| Orchestration (advance stage, assign assessment) | Email delivery infrastructure (consume API) |
| Permissions, workspace settings | Specialized analytics warehouses (aggregate via API) |

## Personas and entry

```text
                    CLIENT APP
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
   Company Admin   Recruiter      Hiring Manager
        │              │              │
        └──────────────┼──────────────┘
                       ↓
                  Dashboard (action-oriented)
                       │
     ┌─────────────────┼─────────────────┐
     ↓                 ↓                 ↓
   Jobs              ATS            Analytics
     │                 │                 │
     ↓                 ↓                 ↓
   JD Builder      Candidates       Reports
     │                 │
     └──────────────┬──┘
                    ↓
             Hiring Workflows
                    │
        ┌───────────┼───────────┐
        ↓           ↓           ↓
   Assessment     Meeting     Email
   Platform       Platform    Service
```

Recruiters land on **action-oriented overview**, not a generic admin home. Primary journeys:

**Dashboard → Jobs → Candidates → Pipeline → Candidate workspace → Automations → Analytics**

We are building the **full product** along that spine (see [roadmap](../roadmap/frontend.md)); delivery is phased by dependency, not by cutting scope.

## Client stack

| Layer | Choice |
|-------|--------|
| Framework | Next.js (App Router), TypeScript |
| Styling | Tailwind CSS, shadcn/ui |
| Server state | TanStack Query |
| Client UI state | Zustand (shell, modals, view prefs) |
| Forms | React Hook Form + Zod |
| Tables | TanStack Table |
| Charts | Recharts |
| i18n | next-intl |
| E2E | Playwright |

## Code organization (this repo)

- **`src/app/`** — routing and layouts only (`(auth)`, `(dashboard)/…`).
- **`src/domains/`** — business areas (jobs, candidates, pipeline, assessments, …). This replaces a separate `src/features/` tree; see [ADR 002](../adr/002-domain-oriented-src-layout.md).
- **`src/components/`** — reusable UI: `ui/`, `layout/`, `navigation/`, plus cross-cutting `hiring/` and `ai/` when shared across domains.
- **`src/shared/`** — cross-domain utilities (tables, forms, permissions helpers).
- **`src/lib/`** — auth, env, low-level clients used by domain `api/` layers.

Domain-oriented boundaries are mandatory; see [folder-structure.md](./folder-structure.md) and `.cursor/rules/domain-oriented-frontend.mdc`.

## Related docs

- [Multi-tenancy (subdomains)](./multi-tenancy.md)
- [Routing and app shell](./routing-and-shell.md)
- [External integrations](./integrations.md)
- [Permissions](./permissions.md)
- [Design system](./design-system.md)
- [AI UX](./ai-ux.md)
- [Frontend roadmap](../roadmap/frontend.md)
