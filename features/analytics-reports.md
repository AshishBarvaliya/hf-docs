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

| Piece | Implementation |
|-------|----------------|
| Domain | Extend `src/domains/analytics/` — `useFunnel`, `useJobHealth`, `useReports` |
| Charts | Recharts; dynamic import for route bundles |
| UI | Dashboard widgets + full analytics pages sharing chart components |
| Query | Longer staleTime for heavy aggregates; explicit refresh button |

- **Permissions:** `analytics.read`; job tab respects `jobs.read`.
- **E2E:** Analytics page loads series; job tab matches job filter param.

## Acceptance criteria

- [ ] Overview and `/analytics` use same metric contracts
- [ ] Job-scoped analytics route
- [ ] Reports export via API URLs or blob fetch
- [ ] Loading/error/empty states on all charts

## References

- [overview-dashboard.md](./overview-dashboard.md)
- [external-integrations.md](./external-integrations.md)
