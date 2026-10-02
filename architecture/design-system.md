# Design system

Build shared primitives **before** duplicating patterns across dozens of screens. Aligns with shadcn/ui + Tailwind tokens in `globals.css`.

**Visual reference:** Static HTML mockups for most product screens live in the Hyreefy workspace folder `designs/` (catalog: `designs/index.html`). See [product-design-mockups.md](./product-design-mockups.md). Implement in `client/`—do not change `designs/` for delivery work.

## Component map

```text
components/ui/           # Button, Input, Select, Dialog, Drawer, Tabs,
                         # Badge, Avatar, Dropdown, Tooltip, …

shared/ or components/data-display/
                         # DataTable, StatCard, Timeline, ActivityFeed, EmptyState

components/hiring/       # CandidateCard, CandidateStatus, PipelineStage,
                         # CandidateScore, JobCard, HiringStage

components/ai/           # AISummary, AIInsight, AIAssistant, AIAction
```

Domain-specific layouts stay in `domains/*/components/` until promoted.

## Data display

- **StatCard** — overview metrics and action counts (“8 need review”).
- **DataTable** — wrap TanStack Table via `shared/tables/` (sorting, selection, pagination).
- **EmptyState** — consistent CTA when lists are empty.
- **Timeline / ActivityFeed** — candidate profile activity tab.

## Hiring visuals

- **Stage badges** — consistent colors for pipeline stages across table and kanban.
- **Score display** — numeric + optional band (e.g. screening score).
- **Verified vs AI** — AI-generated blocks use distinct styling (see [ai-ux.md](./ai-ux.md)).

## Theming

- Use semantic tokens (`background`, `foreground`, `muted`, `destructive`) not raw hex in domains.
- Dark mode via existing Tailwind/shadcn setup when enabled.

Roadmap step 1 in [frontend.md](../roadmap/frontend.md): design system before mass screen build.
