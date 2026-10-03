# Sprint overview visual fidelity — development guide

**Sprint id:** `sprint-overview-visual-fidelity`  
**Feature specs:** [overview-dashboard.md](../features/overview-dashboard.md), [design-system-shell.md](../features/design-system-shell.md)  
**Design canon:** `designs/screens/Overview.html`, `OverviewLoading.html`, `OverviewEmpty.html` ([design-implementation-fidelity.md](../architecture/design-implementation-fidelity.md))

## Goal

Ship recruiter **overview** UI at mockup fidelity on top of the existing `GET /api/v1/analytics/overview` contract—action queue **rows**, summary stat cards with **period selector**, primary page actions, funnel + recent candidates layout, and honest loading/empty states. Server extends the overview API for time-windowed stats (no new tables).

## Scope

### In scope

| Area | Deliverable |
|------|-------------|
| **Design** | Row-based “Needs attention” card, 4-up summary stats, funnel + recent columns, page actions (Add candidate, Create job), loading skeleton, empty workspace |
| **Server** | Optional `period` query (`7d` \| `30d`, default `30d`); `summaryStats[].deltaPercent` vs prior window; Zod + permission-aware omission unchanged |
| **Client** | Period selector wired to query key; visual polish in `domains/analytics/`; deep links unchanged but styled per mockup |
| **Tests** | Server unit + API e2e for period/delta; client unit for mappers/helpers; Playwright overview smoke (actions, period, empty/loading) |

### Non-goals

- `OverviewAI.html` explain drop-off panel (STEP 12 / AI slice)
- `OverviewCards.html` card layout variant
- Interview/assessment queue **real** counts (keep `available: false` until steps 8–9)
- New persistence tables or materialized views
- Drag-and-drop dashboard builder

## Data / DB

**No migration.** Aggregates remain SQL over `jobs`, `candidates`, `candidate_applications`. Document period windows in `domains/server/analytics/README.md` (create if missing).

Dedicated sprint task: `task-overview-fidelity-no-db`.

## API contract (delta)

```
GET /api/v1/analytics/overview?period=30d|7d
```

Extend `overviewSummaryStatSchema`:

| Field | Type | Notes |
|-------|------|--------|
| `deltaPercent` | `number` optional | Week-over-week or period-over-period; omit when not computable |

Invalid `period` → **400** with Zod error shape consistent with other list endpoints.

## Server work plan

1. `overview.schemas.ts` — query schema + extended stat shape; response backward compatible (clients ignore new fields).
2. `analytics.service.ts` — compute stats for selected window; prior window for `deltaPercent` (document formulas in service comments).
3. `analytics.controller.ts` — parse query; pass to service.
4. **Unit:** `analytics.service.spec.ts` — period branches, delta math edge cases (zero prior).
5. **E2e:** `test/analytics-overview.e2e-spec.ts` (or extend existing) — `period`, 401, permission omission.

## Client work plan

1. **Design pass** — diff `Overview.html` vs `overview-page-content.tsx`; list gaps in PR description (spacing, typography, CTA labels).
2. `fetchOverviewMetrics(period)` + query key includes period.
3. Page header: **Add candidate** → `/candidates` (or documented create entry), **Create job** → `/jobs/new`.
4. `OverviewLoading` / `OverviewEmpty` dedicated components or branches (no fake data).
5. Queue rows: count pill, label, CTA button styling, disabled state when `available: false`.
6. **Unit:** period parsing, stat delta display helper, queue row link builder.
7. **Playwright:** `e2e/overview-fidelity.spec.ts` — load overview, toggle period if visible, assert primary actions hrefs, skeleton → content.

## Design audit (`task-overview-design-audit`)

Gaps vs `Overview.html` / loading / empty mockups and how this sprint addresses them:

| Gap | Resolution |
|-----|------------|
| Missing **Add candidate** / **Create job** header CTAs | `overview-page-content.tsx` actions → `/candidates`, `/jobs/new` |
| No **7d / 30d** period control on summary | `OverviewPeriodSelector` + API `?period=` |
| Summary loading was plain text | `OverviewLoading` skeleton layout |
| No dedicated **empty workspace** | `OverviewEmpty` when funnel/recent/jobs are all empty |
| Queue rows lacked disabled **Schedule/Decide** for `available: false` | Rows kept in payload; ghost CTA disabled |
| Stat cards lacked **delta** line | `deltaPercent` from API → `formatOverviewDeltaPercent` |
| Deferred mockup-only (avatar stacks, oldest age, job chips) | Non-goals; counts/links remain server-owned |

## Design work plan

1. Token check: card radius, row dividers, stat card hierarchy vs `Foundations.html`.
2. Stage colors on recent list use `--stage-*`, not primary.
3. Capture before/after screenshots optional; acceptance = checklist + mockup table in feature spec.

## Quality gates (prod-ready)

- [x] `npm run lint` in `server/` and `client/`
- [x] `npm run test` + `npm run test:e2e` (server) for analytics
- [x] `npm run test:unit` + `npm run test:e2e` (client) for overview
- [x] No hardcoded queue counts on success path
- [x] i18n for new strings (`Overview` namespace)

## Task → repo map

| Task id | Repo |
|---------|------|
| `task-overview-fidelity-no-db` | docs + server YAML note |
| `task-overview-period-api` | server |
| `task-overview-server-unit` | server |
| `task-overview-server-e2e` | server |
| `task-overview-design-audit` | client + docs |
| `task-overview-page-actions` | client |
| `task-overview-period-ui` | client |
| `task-overview-states-ui` | client |
| `task-overview-queue-layout` | client |
| `task-overview-client-unit` | client |
| `task-overview-playwright` | client |
