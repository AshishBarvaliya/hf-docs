# Design → implementation fidelity

**Purpose:** When building `client/`, treat static pages under `designs/` as the **visual and information-architecture target**. APIs, permissions, and validation remain authoritative in `features/` and server domains—this doc ties mockup **layout, columns, nav, and states** to those specs and names the **persistence** each screen implies.

**Catalog:** [`designs/index.html`](../../designs/index.html) (78 screens + 5 prototypes). Index: [product-design-mockups.md](./product-design-mockups.md).

## How to use this doc

| When | Do |
|------|-----|
| Starting a feature slice | Open the **canonical mockup** row below; implement that layout first (not alternate variants unless noted). |
| Writing migrations | Add columns/tables listed under **Data implied** before the UI that displays them ([persistence.md](./persistence.md)). |
| Acceptance | Match structure, density, empty/loading/error screens, and role-limited variants named in the feature **Design mockups** table. |

**Variants:** Files like `OverviewCards.html` or `JobsCards.html` are **exploratory**. Unless a feature spec says otherwise, ship the **canonical** row in the tables below.

---

## App shell (all authenticated screens)

| Mockup pattern | Client target | Notes |
|----------------|---------------|--------|
| `#hy-root` sidebar + top bar | `(dashboard)/layout.tsx` | Collapsible sidebar; workspace switcher in header area |
| Nav + badge counts | `nav-config` + server counts | Mockups: Candidates **8**, Assessments **3**, Interviews **4** — same keys as overview queues where applicable |
| ⌘K search, notifications, avatar | Header slot | Search placeholder: “Search candidates/jobs…” |
| **D** theme toggle | `hyreefy-theme` in `localStorage` | Light/dark; tokens from [Foundations.html](../../designs/screens/Foundations.html) |
| Stage badges | `--stage-*` CSS vars | Never reuse `--primary` for pipeline stage color |

**Sidebar → App Router (v1)**

| Label (mockup) | Route | Feature |
|----------------|-------|---------|
| Overview | `/overview` | [overview-dashboard.md](../features/overview-dashboard.md) |
| Jobs | `/jobs` | [jobs.md](../features/jobs.md) |
| Candidates | `/candidates` | [candidates-ats.md](../features/candidates-ats.md) |
| Pipeline | `/pipeline` (or job-scoped) | [hiring-workflows.md](../features/hiring-workflows.md) |
| Assessments | `/assessments` | [external-integrations.md](../features/external-integrations.md) |
| Interviews | `/interviews` | [interviews.md](../features/interviews.md) |
| Analytics | `/analytics` | [analytics-reports.md](../features/analytics-reports.md) |
| Automations | `/automations` | [automations.md](../features/automations.md) |
| Email | `/email` | [emails.md](../features/emails.md) |
| Settings (footer) | `/settings/...` | [workspace.md](../features/workspace.md), [rbac.md](../features/rbac.md) |

Spec: [design-system-shell.md](../features/design-system-shell.md), [routing-and-shell.md](./routing-and-shell.md).

---

## Screen catalog by product area

Paths are from `ms/` workspace: `designs/screens/<file>`.

### Foundations (1)

| File | Feature | Canonical? |
|------|---------|------------|
| `Foundations.html` | design-system-shell | Yes — tokens, stage palette, components |

### Overview (5)

| File | Feature | Canonical? |
|------|---------|------------|
| `Overview.html` | overview-dashboard | **Yes** — action queue **rows** |
| `OverviewCards.html` | overview-dashboard | Variant only |
| `OverviewAI.html` | overview-dashboard, ai-skill-analyzers, analytics-reports | Explain drop-off panel |
| `OverviewLoading.html` | overview-dashboard | Loading |
| `OverviewEmpty.html` | overview-dashboard | Empty workspace |

**Data implied:** Aggregates only until `jobs`, `candidate_applications`, interviews exist; queue definitions must match deep links in mockup `title` attributes.

### Candidates ATS (10)

| File | Feature | Canonical? |
|------|---------|------------|
| `CandTable.html` | candidates-ats | **Yes** — table view |
| `CandKanban.html` | candidates-ats | **Yes** — board view |
| `CandSelected`, `CandFiltered`, `CandDrag`, `CandKanbanCompact` | candidates-ats | Interaction / layout states |
| `CandTable1280.html` | candidates-ats | Responsive reference |
| `CandLoading`, `CandEmpty`, `CandNoMatch` | candidates-ats | States |

**Data implied:** List rows are **applications** (candidate + job + stage + score + experience + source + applied). See `candidate_applications` in [candidates-ats.md](../features/candidates-ats.md).

### Candidate profile (12)

| File | Feature | Canonical? |
|------|---------|------------|
| `Profile.html` | candidate-profile | **Yes** — Summary **layout A** (sidebar) |
| `ProfileGrid.html` | candidate-profile | Variant B — do not ship first |
| `ProfileResume`, `ProfileNoResume`, `ProfileAssessments`, `ProfileEmails`, `ProfileActivity` | candidate-profile + integrations | Tab panels |
| `ProfileReject`, `ProfileLoading`, `ProfileHM`, `ProfileNoAI`, `Profile1280` | candidate-profile, rbac, ai-skill-analyzers | States / roles |
| `EmailCompose.html`, `AssignAssessment.html`, `InterviewReport.html` | emails, external-integrations, interviews, ai-skill-analyzers | Modals / report tab |

Tabs in mockups: **Summary, Resume, Assessments, Emails, Activity** (no separate “Analysis” tab in HTML — analyzer content appears in Summary / `InterviewReport.html` until Analysis tab ships per spec).

### Jobs (11)

| File | Feature | Canonical? |
|------|---------|------------|
| `Jobs.html` | jobs | **Yes** — status tabs + **table** |
| `JobsCards.html` | jobs | Variant only |
| `JobWorkspace.html` | jobs | **Yes** — workspace overview tab |
| `JobDraft`, `JobViewer`, `JobsViewer`, `JobTabsOverflow`, `JobWorkspaceAI`, `JobAutomation` | jobs + rbac + ai + automations | States / tabs |
| `JobsLoading`, `JobsEmpty`, `JobsNoPaused` | jobs | States |

**Job workspace tabs (mockup order):** Overview, JD, Candidates, Pipeline, Assessments, Interviews, Emails, Automation, Analytics, Settings — overflow **More** at 1280px (`JobTabsOverflow.html`).

### JD builder (6)

| File | Feature | Canonical? |
|------|---------|------------|
| `Main.html` | jd-builder | Paste JD entry (`/jobs/new`) |
| `Generate.html`, `Parsing.html`, `Editor.html`, `Publish.html`, `JDTab.html` | jd-builder, ai-skill-analyzers | AI + async + live JD tab |

### Sign in & access (6)

| File | Feature | Canonical? |
|------|---------|------------|
| `Login.html`, `LoginError.html`, `LoginBusy.html`, `LoginExpired.html` | auth | Sign-in states |
| `TenantNotFound.html` | auth, multi-tenancy | Unknown subdomain |
| `NoAccess.html` | auth, rbac, permissions-audit | In-app 403 |

### Workspace settings (8)

| File | Feature | Canonical? |
|------|---------|------------|
| `SettingsGeneral.html` | workspace | General + **settings rail** pattern |
| `SettingsCompany.html`, `SettingsDefaults.html`, `SettingsCulture.html`, `SettingsCultureReadOnly.html` | workspace, rbac | Company, defaults, culture |
| `SettingsMembers.html`, `SettingsInvite.html`, `SettingsRoles.html` | workspace, rbac | People |

**Settings rail (from `SettingsGeneral.html`):** Workspace → General, Company profile, Culture & values, Job defaults · People → Members, Roles & permissions · Hiring → Pipeline templates, AI analyzers · Connections → Integrations, Email sending · Account → Billing (billing non-goal in v1; rail item disabled/hidden until product ships).

### Hiring workflows (6)

| File | Feature | Canonical? |
|------|---------|------------|
| `WorkflowTemplates.html`, `WorkflowEditor.html`, `WorkflowAddRound.html`, `WorkflowPublish.html` | hiring-workflows | Template gallery + editor |
| `JobPipeline.html`, `JobCandidates.html` | hiring-workflows, jobs, candidates-ats | Job tabs |

### Assessments & interviews (6)

| File | Feature | Canonical? |
|------|---------|------------|
| `SettingsIntegrations.html` | external-integrations | Integration health |
| `Assessments.html`, `AssignAssessment.html`, `ProfileAssessments.html` | external-integrations, candidate-profile | Assessments |
| `Interviews.html`, `ScheduleInterview.html`, `InterviewReport.html` | interviews, ai-skill-analyzers | Scheduling + AI report |

### Email & automations (7)

| File | Feature | Canonical? |
|------|---------|------------|
| `EmailTemplates.html`, `EmailTemplateEdit.html`, `EmailSent.html` | emails | Workspace email admin |
| `EmailCompose.html`, `ProfileEmails.html` | emails, candidate-profile | Composer + history |
| `AutomationRules.html`, `AutomationRuleEdit.html`, `JobAutomation.html` | automations | Rules + job tab |

---

## Prototypes (`designs/prototypes/`)

Interactive review toolbars (theme, width, UI state). Use for exploration; **ship static canonical screens** above unless product explicitly chooses a prototype-only pattern.

| File | Feature |
|------|---------|
| `hiring-os-foundations.html` | design-system-shell |
| `hiring-os-overview.html` | overview-dashboard |
| `hiring-os-jobs.html` | jobs |
| `hiring-os-candidates.html` | candidates-ats |
| `hiring-os-candidate-profile.html` | candidate-profile |

---

## Cross-cutting data entities (mockup-driven)

These tables are required for mockup-faithful lists and tabs—not optional “phase 2” shapes.

| Entity | First needed for | Documented in |
|--------|------------------|---------------|
| `jobs` | Jobs list, job workspace, JD | [jobs.md](../features/jobs.md), [jd-builder.md](../features/jd-builder.md) |
| `candidate_applications` | Candidates table/kanban (job column, stage, score, applied) | [candidates-ats.md](../features/candidates-ats.md) |
| `workflows` / stages | Kanban columns, job Pipeline tab | [hiring-workflows.md](../features/hiring-workflows.md) |
| Activity, emails, assessments, interviews, analyzer reports | Profile tabs | respective feature specs |

---

## Related

- [product-design-mockups.md](./product-design-mockups.md) — format and do-not-edit policy
- [design-system.md](./design-system.md) — component mapping into `client/`
- [features/README.md](../features/README.md) — roadmap order
