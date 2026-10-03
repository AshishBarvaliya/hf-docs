# Design system & app shell

**Roadmap steps:** 1 (design system), 2 (shell)  
**Routes:** `(dashboard)/layout.tsx`, shared components  
**Domains:** none (presentation + layout)

## Product behavior

Consistent UI across all hiring surfaces: shadcn primitives, hiring-specific cards/badges, data tables, empty states, AI affordances, and the persistent dashboard chrome (header, sidebar, search slot, notifications, avatar).

## Plan

1. **Tokens & primitives** — Extend shadcn set (Dialog, Drawer, Tabs, Select, Badge, Avatar, Dropdown, Tooltip) before new screens.
2. **Data display** — `StatCard`, `EmptyState`, shared `DataTable` wrappers (TanStack Table).
3. **Hiring & AI** — `JobCard`, `CandidateStatus`, `PipelineStage`, `AISummary`, `AIAction`.
4. **Shell** — Sidebar nav aligned to full product areas; permission-filtered items; job context header pattern for nested routes.
5. **Dependencies** — Workspace auth session for avatar; **RBAC** ([rbac.md](../features/rbac.md)) for permission-filtered nav before STEP 2 shell.

## Dev

Client-only feature — no orchestration API or database tables.

### Contract

N/A (presentation and app shell only). Nav targets and permission keys align with [rbac.md](./rbac.md) and [permissions.md](../architecture/permissions.md).

### Data

N/A.

### Server

N/A.

### Client

| Area | Location |
|------|----------|
| Primitives | `src/components/ui/` |
| Hiring / AI | `src/components/hiring/`, `src/components/ai/` |
| Tables | `src/shared/tables/` — extend for pagination/virtualization |
| Layout | `src/components/layout/`, `src/components/navigation/` |
| Dashboard layout | `src/app/(dashboard)/layout.tsx` |
| Shell UI state | `src/shared/stores/ui-store.ts` (sidebar, view prefs) |

i18n: nav labels in `messages/`. Lazy-load heavy chart bundles at route level, not in layout.

### Tests

- **Client unit:** `nav-config.test.ts` — permission-filtered nav
- **Client e2e:** Dashboard layout renders; nav links resolve (smoke when routes change)

## Acceptance criteria

- [x] Dashboard shell chrome matches mockups (240px sidebar, 48px header, ⌘K affordance, collapse, `D` theme, nav queue badges when overview API returns counts)
- [ ] Full nav set present (Overview, Jobs, Candidates, Pipeline, Assessments, Interviews, Analytics, Automations, Email, Settings)
- [ ] Reusable StatCard / EmptyState used on overview and list pages
- [ ] Hiring badges consistent between table and kanban
- [ ] AI blocks use shared styling from [ai-ux.md](../architecture/ai-ux.md)

## Design mockups

Paths from `ms/` workspace. [Catalog & analysis](../architecture/product-design-mockups.md).

| File | Notes |
|------|--------|
| `designs/screens/Foundations.html` | Tokens (“Roster indigo”), `--stage-*` palette, component gallery, token CSS export |
| `designs/prototypes/hiring-os-foundations.html` | Interactive foundations + theme/width toolbar |
| Any `designs/screens/*.html` with sidebar | App shell: workspace header, nav badges, ⌘K search, top bar |

**Implementation note:** Prefer promoting patterns from Foundations into `client/src/components/ui/` and layout components—not copying raw HTML.

## UI fidelity (canonical mockups)

| Area | Mockup source | Client behavior |
|------|---------------|-----------------|
| Tokens & stages | `Foundations.html` | Roster indigo `--primary`; pipeline `--stage-*`; Geist / Geist Mono |
| Shell | Any screen with sidebar | Workspace switcher, collapsible nav, ⌘K, notifications, avatar; nav badge counts on Candidates / Assessments / Interviews when API provides |
| Settings layout | `SettingsGeneral.html` | **212px settings rail** + content max ~960px; section groups: Workspace, People, Hiring, Connections, Account — routes in [design-implementation-fidelity.md](../architecture/design-implementation-fidelity.md) |

## References

- [design-system.md](../architecture/design-system.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
- [routing-and-shell.md](../architecture/routing-and-shell.md)
