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

### Shipped slice (`sprint-overview-visual-fidelity`, closed 2026-10-03)

**Visual fidelity** on canonical `Overview.html`: page actions, period selector (7d/30d), queue row layout, loading/empty states; server adds `?period=` and optional `deltaPercent` on summary stats (no new tables). Dev guide: [sprint-overview-visual-fidelity-development-guide.md](../sprint/sprint-overview-visual-fidelity-development-guide.md). Checklist: [sprint-overview-visual-fidelity-progress-checklist.md](../sprint/sprint-overview-visual-fidelity-progress-checklist.md).

**Not this sprint:** Overview AI panel (`OverviewAI.html`); Cards layout variant.

## Dev

### Contract

| Area | Details |
|------|---------|
| Overview metrics | `GET /api/v1/analytics/overview` — workspace-scoped aggregates; optional `period=7d\|30d` (default `30d`) in `sprint-overview-visual-fidelity` |
| Response | `actionQueues[]` (`id`, `count`, `permission`, `available`), `summaryStats[]` (`id`, `value`, `permission`, `available`, optional `deltaPercent`), `funnelStages[]` (`stage`, `count`), `recentCandidates[]` (`id`, `name`, `role`, `stage`). Sections the caller lacks permission for are omitted. Interview/assessment queues use `available: false` until those domains exist. |
| Permissions | Widgets respect `candidates.read`, `jobs.read`, `interviews.schedule`, etc.; server omits gated sections from the payload |
| Redirect | Authenticated `/` → `/overview` in `(dashboard)` group |

### Data

Read-only aggregates over existing entities (`candidates`, `jobs`, interviews when tables exist). No new persistence table in the overview slice; metric SQL/views documented in the analytics server domain when implemented.

### Server

- `server/src/domains/analytics/` — `GET /api/v1/analytics/overview`; JWT workspace scope; sections omitted when caller lacks the section permission

### Client

- Route: `src/app/(dashboard)/overview/page.tsx`
- `domains/analytics/hooks/useOverviewMetrics.ts` (or split hooks); query keys in `analytics/api/`
- UI: `StatCard`, action rows, `PipelineChart`, recent list; i18n namespace `Overview`
- TanStack Query stale time ~30–60s; refetch on window focus for counts

### Tests

- **Client unit:** `overview-metrics.test.ts`, period/delta helpers (sprint-overview-visual-fidelity), `nav-config.test.ts` (permission-filtered shell)
- **Client e2e:** `e2e/overview-fidelity.spec.ts` — page actions, period selector, loading/empty; recruiter queue rows and deep links
- **Server unit:** `analytics.service.spec.ts` — period windows, `deltaPercent` edge cases
- **Server API:** `test/analytics-overview.e2e-spec.ts` (or extend existing) — `period`, 400, 401/403

## Acceptance criteria

- [x] Default landing route for dashboard users
- [x] Queue counts and funnel from `GET /api/v1/analytics/overview` (no client hardcoded metrics on success)
- [x] Action queues with deep links and empty states (server-owned counts; no client placeholders)
- [x] Funnel + recent candidates widgets (chart uses API `funnelStages`)
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

## UI fidelity (canonical mockups)

Ship **`Overview.html`** (queue **rows**), not `OverviewCards.html`.

| Section | Mockup source | Client behavior |
|---------|---------------|-----------------|
| Page actions | `Overview.html` | **Add candidate**, **Create job** (primary → `/jobs/new` → JD entry) |
| Needs attention | `Overview.html` | Card with row queues: (1) candidates need review → `/candidates?queue=needs-review`, (2) interviews to schedule → `/interviews?queue=to-schedule`, (3) assessments awaiting decision → `/candidates?stage=assessment&queue=decision`, (4) jobs with low flow → `/jobs?filter=low-flow`. Each row: count, label, avatar preview, oldest age, CTA (Review / Schedule / Decide / Open) |
| Summary stats | `Overview.html` | 4-up cards: Open jobs, Candidates applied, Interviews scheduled, Offers extended — period **Last 30 days** selector; week-over-week delta where mockup shows |
| Funnel + recent | `Overview.html` | Hiring funnel snippet + **Recent candidates** list linking to profile |
| AI | `OverviewAI.html` | Explain drop-off side panel (async; [ai-skill-analyzers.md](./ai-skill-analyzers.md)) |
| States | `OverviewLoading.html`, `OverviewEmpty.html` | Skeleton; empty workspace onboarding tone |

Server owns queue definitions and counts; client must not hardcode mock numbers.

## References

- [routing-and-shell.md](../architecture/routing-and-shell.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
