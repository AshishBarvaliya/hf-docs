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

| Area | Location |
|------|----------|
| Primitives | `src/components/ui/` |
| Hiring / AI | `src/components/hiring/`, `src/components/ai/` |
| Tables | `src/shared/tables/` (existing), extend for pagination/virtualization |
| Layout | `src/components/layout/`, `src/components/navigation/` |
| Dashboard layout | `src/app/(dashboard)/layout.tsx` |
| Shell UI state | `src/shared/stores/ui-store.ts` (sidebar, view prefs) |

- **i18n:** Nav labels and shell strings in `messages/`; no hardcoded sidebar text.
- **Tests:** Storybook optional; at minimum smoke E2E that dashboard layout renders and nav links resolve.
- **Performance:** Lazy-load heavy chart bundles at route level, not in layout.

## Acceptance criteria

- [ ] Full nav set present (Overview, Jobs, Candidates, Pipeline, Assessments, Interviews, Analytics, Automations, Email, Settings)
- [ ] Reusable StatCard / EmptyState used on overview and list pages
- [ ] Hiring badges consistent between table and kanban
- [ ] AI blocks use shared styling from [ai-ux.md](../architecture/ai-ux.md)

## References

- [design-system.md](../architecture/design-system.md)
- [routing-and-shell.md](../architecture/routing-and-shell.md)
