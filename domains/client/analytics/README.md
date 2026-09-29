# Analytics domain

**Code:** `src/domains/analytics/`

## Purpose

Hiring funnel charts, dashboard metrics, and job-level analytics views. Aggregates from analytics API (not raw warehouse queries in the client).

## Public API

- `PipelineChart` (and dynamic import wrapper if used)

## UI surfaces

| Surface | Status |
|---------|--------|
| Home pipeline chart | Done |
| Overview funnel | Planned |
| Job analytics tab | Planned |
| Reports | Planned |

## Notes

- Prefer Recharts; lazy-load heavy chart bundles on dashboard routes.
- Align metrics definitions with backend analytics API contracts before building new charts.

## References

- [overview-dashboard.md](../../features/overview-dashboard.md)
