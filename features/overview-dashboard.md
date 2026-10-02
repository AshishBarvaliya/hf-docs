# Overview dashboard

**Route:** `(dashboard)/overview` (default after login)  
**Personas:** Recruiter (primary), Hiring Manager, Company Admin  
**Domains:** `analytics`, `candidates`, `jobs`, `meetings`, `assessments` (read-only aggregates)

## Product behavior

Recruiter home is **action-oriented**: prioritized queues (“8 need review”, “4 interviews to schedule”) above summary stats. Includes hiring funnel snippet and recent candidates. Not a generic admin grid.

**Replaces** the interim `/` spike ([foundation-spike.md](./foundation-spike.md)).

## Plan

1. **API contracts** — Overview endpoint or parallel queries: action counts, funnel series, recent candidates, stat totals. Backend owns definitions of “needs review” and “low flow”.
2. **Phase A** — Shell + static layout with skeleton loaders; wire real APIs as available.
3. **Phase B** — Deep links to filtered `/candidates`, `/jobs`, `/interviews` with query params preserved in docs.
4. **Phase C** — Personalization (greeting, timezone) and permission-filtered widgets.
5. **Dependencies** — [auth.md](./auth.md) + [rbac.md](./rbac.md) (STEP 1.5–1.6); dashboard layout (step 2); jobs/candidates list routes for links (step 4–5).

## Dev

### Contract

| Area | Details |
|------|---------|
| Overview metrics | `GET /api/v1/analytics/overview` (or parallel reads) — action queue counts, funnel series, recent candidates, stat totals; definitions owned by server |
| Permissions | Widgets respect `candidates.read`, `jobs.read`, `interviews.schedule`, etc. |
| Redirect | Authenticated `/` → `/overview` in `(dashboard)` group |

### Data

Read-only aggregates over existing entities (`candidates`, `jobs`, interviews when tables exist). No new persistence table in the overview slice; metric SQL/views documented in the analytics server domain when implemented.

### Server

- `server/src/domains/analytics/` (planned) — overview query service; JWT + workspace scope + `PermissionsGuard`
- Reuse candidates list contract until dedicated overview endpoint ships

### Client

- Route: `src/app/(dashboard)/overview/page.tsx`
- `domains/analytics/hooks/useOverviewMetrics.ts` (or split hooks); query keys in `analytics/api/`
- UI: `StatCard`, action rows, `PipelineChart`, recent list; i18n namespace `Overview`
- TanStack Query stale time ~30–60s; refetch on window focus for counts

### Tests

- **Client unit:** `overview-metrics.test.ts`, `nav-config.test.ts` (permission-filtered shell)
- **Client e2e:** Recruiter sees action item or empty state; deep link to candidates/jobs when routes exist
- **Server API:** e2e for overview endpoint when added; 401/403 without auth or permission

## Acceptance criteria

- [x] Default landing route for dashboard users
- [x] Action queues with deep links and empty states
- [x] Funnel + recent candidates widgets
- [x] Permission-aware widget visibility
- [x] Full i18n coverage

## Non-goals

- Drag-and-drop dashboard builder (future)

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/Overview.html` | Action queues as rows (default) |
| `designs/screens/OverviewCards.html` | Queues as cards variant |
| `designs/screens/OverviewAI.html` | “Explain drop-off” AI panel |
| `designs/screens/OverviewLoading.html` | Loading skeleton |
| `designs/screens/OverviewEmpty.html` | Empty workspace |
| `designs/prototypes/hiring-os-overview.html` | Interactive overview + state toolbar |

## References

- [routing-and-shell.md](../architecture/routing-and-shell.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
