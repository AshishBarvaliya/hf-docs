# Sprint 1 remediation — server-authoritative overview

**Status:** done  
**Created:** 2026-10-03  
**Feature spec:** [overview-dashboard.md](../../features/overview-dashboard.md)

## Summary

Replace client placeholder overview metrics with `GET /api/v1/analytics/overview` aggregating workspace-scoped candidates and jobs. Client renders loading, error, empty, and unavailable sections honestly.

## Out of scope

- Overview AI panel, period selector, mockup-perfect page actions (design-fidelity backlog)
- Interview/assessment queue counts until those domains exist (return `available: false`)

## Server

- [x] `server/src/domains/analytics/` — overview service + controller
- [x] Zod response schema; JWT workspace scope; permission-aware section omission
- [x] **API unit tests** — overview service counts
- [x] **API e2e** — 401, acme vs beta isolation, interviewer omits candidate sections
- [x] Register `AnalyticsModule` in `app.module.ts`

## Client

- [x] `fetchOverviewMetrics` + Zod parse; wire `useOverviewMetrics`
- [x] Remove placeholder counts; `PipelineChart` accepts `funnelStages` prop
- [x] **UI unit tests** — map/filter helpers against API-shaped payloads

## Contract / API

`GET /api/v1/analytics/overview` — Bearer required. Response: `actionQueues`, `summaryStats`, `funnelStages`, `recentCandidates`; each queue/stat includes `permission` and `available` when backend cannot compute.

## Acceptance criteria

- [x] No fabricated counts on overview when API succeeds
- [x] API failure shows error state (not fake success)
- [x] Funnel chart uses API `funnelStages`
