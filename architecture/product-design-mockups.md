# Product design mockups (`designs/`)

Static HTML **visual reference** for Hyreefy screens. Behavior, APIs, and permissions remain authoritative in `features/` and server specs—use these mockups for layout, copy tone, states, and visual patterns when implementing `client/`.

## Location

| Workspace | Path |
|-----------|------|
| **Hyreefy `ms/`** (recommended) | `designs/` at workspace root (sibling to `docs/`, `client/`, `server/`) |
| **Docs-only checkout** | Not in this repo; open the full `ms/` workspace or copy `designs/` alongside `docs/` |

There is **no** `designs/` tree inside `docs/` or `client/`.

## Do not edit mockups for delivery

- Treat `designs/` as **read-only reference** during product implementation.
- Ship UI changes in **`client/`** (and update feature specs when behavior changes).
- Do not treat exported HTML as the runtime app or sync implementation back into `designs/`.

## HTML analysis (what these files are)

| Aspect | Detail |
|--------|--------|
| **Format** | Self-contained static pages; no build step. Most app screens are **1440×900** fixed canvases; Foundations and a few editors are taller. Some layouts include **1280** width variants. |
| **Styling** | CSS custom properties aligned with **shadcn semantics** (`--background`, `--primary`, `--muted-foreground`, etc.). Brand proposal: **“Roster indigo”** on `--primary`; pipeline uses separate **`--stage-*`** hues so brand is never confused with status (documented in `Foundations.html`). |
| **Fonts** | **Geist** / Geist Mono via Google Fonts. |
| **Assets** | Shared `designs/assets/hyreefy.css` plus area bundles (`candidates.css`, `jobs.css`, `overview.css`, `foundations.css`, `candidate-profile.css`). |
| **App chrome** | Typical screen: `#hy-root` → collapsible **sidebar** (workspace switcher, nav with action counts), **top bar** (title, ⌘K search, notifications, avatar), main content. Nav links wire mock routes to sibling `.html` files under `screens/`. |
| **Foundations** | Single long reference page: palette rationale, stage colors, typography/spacing, component gallery, exported token CSS, optional **CVD** preview filters. |
| **Prototypes** | Five files under `prototypes/` embed tokens inline and add **review toolbars** (theme, width, UI states)—closer to exploratory UX than pixel-frozen exports. |
| **State coverage** | Naming convention: base screen + suffixes for **Loading**, **Empty**, **Error**, role-limited (**Viewer**, **HM**, **ReadOnly**), **AI** panels, and interaction moments (**Drag**, **Reject** dialog, save **conflict**). |
| **Interactivity** | Mostly presentational; light JS for theme toggle (**D**), sidebar collapse, and prototype toolbars—not production behavior. |

Paths below are from the **`ms/` workspace root** (e.g. `designs/screens/Login.html`).

## Contents

```text
designs/
  index.html          # Catalog of all screens (grouped by product area)
  screens/            # 78 static pages
  prototypes/         # 5 interactive HTML prototypes
  assets/             # Shared CSS (tokens, per-area styles)
```

### Interactive prototypes (`prototypes/`)

| File | Feature spec |
|------|----------------|
| `designs/prototypes/hiring-os-foundations.html` | [design-system-shell.md](../features/design-system-shell.md) |
| `designs/prototypes/hiring-os-overview.html` | [overview-dashboard.md](../features/overview-dashboard.md) |
| `designs/prototypes/hiring-os-jobs.html` | [jobs.md](../features/jobs.md) |
| `designs/prototypes/hiring-os-candidates.html` | [candidates-ats.md](../features/candidates-ats.md) |
| `designs/prototypes/hiring-os-candidate-profile.html` | [candidate-profile.md](../features/candidate-profile.md) |

### Feature spec → mockup files

Each feature doc has a **Design mockups** section with the same paths and notes. Index:

| Feature | Primary `designs/screens/` files (see feature doc for full table) |
|---------|-------------------------------------------------------------------|
| [design-system-shell.md](../features/design-system-shell.md) | `Foundations.html` + all screens for shell chrome |
| [auth.md](../features/auth.md) | `Login*.html`, `TenantNotFound.html`, `NoAccess.html` |
| [rbac.md](../features/rbac.md) | `SettingsRoles.html`, viewer/HM/read-only variants |
| [overview-dashboard.md](../features/overview-dashboard.md) | `Overview*.html` |
| [workspace.md](../features/workspace.md) | `Settings*.html` |
| [jobs.md](../features/jobs.md) | `Jobs*.html`, `JobWorkspace*.html`, `JobDraft.html`, `JobViewer.html`, `JobTabsOverflow.html` |
| [jd-builder.md](../features/jd-builder.md) | `Main.html`, `Generate.html`, `Parsing.html`, `Editor.html`, `Publish.html`, `JDTab.html` |
| [candidates-ats.md](../features/candidates-ats.md) | `Cand*.html` |
| [candidate-profile.md](../features/candidate-profile.md) | `Profile*.html`, `EmailCompose.html`, `AssignAssessment.html` |
| [hiring-workflows.md](../features/hiring-workflows.md) | `Workflow*.html`, `JobPipeline.html`, `JobCandidates.html` |
| [external-integrations.md](../features/external-integrations.md) | `Assessments.html`, `SettingsIntegrations.html`, `AssignAssessment.html` |
| [interviews.md](../features/interviews.md) | `Interviews.html`, `ScheduleInterview.html`, `InterviewReport.html` |
| [emails.md](../features/emails.md) | `EmailTemplates.html`, `EmailTemplateEdit.html`, `EmailCompose.html`, `EmailSent.html` |
| [automations.md](../features/automations.md) | `AutomationRules.html`, `AutomationRuleEdit.html`, `JobAutomation.html` |
| [ai-skill-analyzers.md](../features/ai-skill-analyzers.md) | `OverviewAI.html`, `JobWorkspaceAI.html`, `ProfileNoAI.html`, `InterviewReport.html`, `Generate.html` |
| [analytics-reports.md](../features/analytics-reports.md) | `OverviewAI.html`, `JobWorkspaceAI.html` (explain/drop-off only; no full analytics dashboard export yet) |
| [permissions-audit.md](../features/permissions-audit.md) | `NoAccess.html`, `ProfileActivity.html` (activity/audit tone; no dedicated audit log page) |

## How to view

1. Open `designs/index.html` in a browser, or serve the folder with any static file server from `ms/`.
2. On catalog and screen pages, press **`D`** to toggle light/dark; the choice is stored in `localStorage` (`hyreefy-theme`).

Catalog copy in `index.html` describes dimensions and variants (loading, empty, role-limited views, AI affordances, etc.).

## When implementing the client

| Use mockups for | Use specs / code for |
|-----------------|----------------------|
| Page structure, density, empty/loading/error states | Routes, data fetching, validation |
| Hiring visuals (badges, kanban vs table) | Pipeline stage IDs and workflow rules from API |
| AI block placement and styling cues | [ai-ux.md](./ai-ux.md), async job behavior |
| Settings and workflow editor layout | RBAC and workspace settings APIs |

See also [design-system.md](./design-system.md) and [design-system-shell.md](../features/design-system-shell.md) for what to build in `client/src/components/`.
