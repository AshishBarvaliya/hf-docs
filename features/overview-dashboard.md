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

| Piece | Implementation |
|-------|----------------|
| Route | `src/app/(dashboard)/overview/page.tsx` — Server Component wrapper; client widgets as needed |
| Data | `domains/analytics/hooks/useOverviewMetrics.ts` (or split hooks per widget); query keys in `analytics/api/` |
| UI | `StatCard`, action list rows, `PipelineChart`, compact `CandidatesTable` or custom recent list |
| Permissions | Hide action rows when user lacks `candidates.read`, `interviews.schedule`, etc. |
| i18n | Namespace `Overview` + reuse domain strings where shared |

- **TanStack Query:** Stale times tuned for dashboard (e.g. 30–60s); refetch on window focus for counts.
- **E2E:** Recruiter sees at least one action item or empty state; click-through to candidates/jobs works.
- **Redirect:** Root `/` → `/overview` when authenticated (in `(dashboard)` group).

## Acceptance criteria

- [ ] Default landing route for dashboard users
- [ ] Action queues with deep links and empty states
- [ ] Funnel + recent candidates widgets
- [ ] Permission-aware widget visibility
- [ ] Full i18n coverage

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
