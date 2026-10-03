# Analytics domain (server)

**Client spec:** [overview-dashboard.md](../../features/overview-dashboard.md)  
**Module:** `server/src/domains/analytics/`

## Current scope

- `GET /api/v1/analytics/overview` — bearer JWT; workspace-scoped aggregates for dashboard widgets (`actionQueues`, `summaryStats`, `funnelStages`, `recentCandidates`). Sections omitted when caller lacks the section permission. Interview/assessment queues may return `available: false` until those product areas ship.

## Overview period (`?period=7d|30d`)

| Query | Default | Invalid |
|-------|---------|---------|
| `period` | `30d` | **400** (Zod validation, same shape as other list endpoints) |

### Time windows

For a selected period length `N` days (`7` or `30`):

- **Current window:** `[now − N days, now)` — used for `candidates-applied` **value** (count of candidates with `created_at` in the window).
- **Prior window:** `[now − 2N days, now − N days)` — compared to the current window for `deltaPercent`.

`deltaPercent` on `summaryStats` entries:

- Formula: `round((current − prior) / prior × 100)` when `prior > 0`.
- **Omitted** when the prior-window count is zero (not computable).

### Per-stat semantics

| Stat id | `value` | `deltaPercent` basis |
|---------|---------|----------------------|
| `open-jobs` | Count of active jobs (snapshot) | Jobs **created** in current vs prior window |
| `candidates-applied` | Candidates **created** in current window | Same entity counts in prior window |
| `offers-pending` | Candidates in **Offer** stage (snapshot) | Offer-stage candidates **created** in each window |
| `interviews-scheduled` | Placeholder `0` when `available: false` | Not computed until interviews domain ships |

Action queues, funnel stages, and recent candidates are **not** filtered by `period` in this sprint; they reflect current pipeline state.

## Data / DB

**No migration.** Aggregates are SQL over existing `jobs`, `candidates`, and (when used) `candidate_applications`.

## Tests

- Unit: `analytics.service.spec.ts`, `overview-period` helpers
- API e2e: `test/analytics.e2e-spec.ts` — `period`, invalid period **400**, **401** without token, permission omission (sections omitted, not **403** on the route)
