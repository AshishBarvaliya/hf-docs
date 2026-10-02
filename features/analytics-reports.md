# Analytics & reports

**Routes:** `(dashboard)/analytics`, `(dashboard)/jobs/[jobId]/analytics`, overview widgets  
**Domain:** `analytics`  
**Roadmap step:** 11

## Product behavior

Full **analytics** area: hiring funnel, pipeline conversion, time-in-stage, job health (“low candidate flow”), recruiter activity, exportable reports. Overview dashboard consumes subset of same metrics—not a separate toy chart.

## Plan

1. **API** — Metric definitions owned by analytics backend; date range, job filter, compare periods if supported.
2. **Phase A** — Replace sample pipeline chart with API-driven funnel on overview + analytics home.
3. **Phase B** — Job analytics tab (funnel, source breakdown if available).
4. **Phase C** — Reports list, CSV/PDF download links from API.
5. **Phase D** — Embedded AI “explain drop-off” on funnel ([ai-ux.md](../architecture/ai-ux.md)).
6. **Dependencies** — Jobs and pipeline data stable; integration client from step 8+.

**Note:** Extend existing `PipelineChart` spike to production hooks—not a separate MVP chart ([foundation-spike.md](./foundation-spike.md)).

## Dev

### Contract

| Area | Details |
|------|---------|
| Funnel | `GET /api/v1/analytics/funnel` — workspace or `?jobId=` |
| Job health | `GET /api/v1/analytics/jobs/:id/health` |
| Reports | `GET /api/v1/analytics/reports` + export URLs |
| Permissions | `analytics.read`; job routes also require `jobs.read` |

### Data

Aggregate queries over `candidates`, `jobs`, applications — no separate fact table required for v1; materialized views optional later.

### Server

- `server/src/domains/analytics/` — SQL/services for metrics; same definitions as overview widgets

### Client

- Extend `src/domains/analytics/` — `useFunnel`, `useJobHealth`, `useReports`
- Recharts via dynamic import; shared with [overview-dashboard.md](./overview-dashboard.md)

### Tests

- **Server API:** scoped series, 403 without `analytics.read`
- **Client unit:** chart data shaping
- **Client e2e:** `/analytics` loads; job tab respects `jobId` param

## Acceptance criteria

- [ ] Overview and `/analytics` use same metric contracts
- [ ] Job-scoped analytics route
- [ ] Reports export via API URLs or blob fetch
- [ ] Loading/error/empty states on all charts

## Design mockups

No dedicated `/analytics` dashboard export yet. Related AI explain patterns:

| File | Notes |
|------|--------|
| `designs/screens/OverviewAI.html` | Drop-off explanation on overview |
| `designs/screens/JobWorkspaceAI.html` | Drop-off explanation on job workspace |

Nav item “Analytics” appears in shell mockups but routes are placeholders (`#`).

## References

- [overview-dashboard.md](./overview-dashboard.md)
- [external-integrations.md](./external-integrations.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
