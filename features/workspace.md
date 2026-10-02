# Workspace & company

**Routes:** `(dashboard)/settings`, onboarding flows, member invites  
**Domain:** `workspace` (to implement)  
**Roadmap step:** 3  
**Sprint 2 guide:** [sprint-2-development-guide.md](../sprint/sprint-2-development-guide.md)  
**Client / server domain docs:** [domains/client/workspace/README.md](../domains/client/workspace/README.md), [domains/server/workspaces/README.md](../domains/server/workspaces/README.md)  
**Tenancy:** [multi-tenancy.md](../architecture/multi-tenancy.md), [ADR 004](../adr/004-subdomain-multi-tenancy.md)

## Product behavior

Each **customer tenant** (subdomain) has one **primary workspace** in v1: company profile, branding basics, members, roles, billing entry (admin), and integration settings hooks. Recruiters always work inside the workspace implied by the URL they used to sign in (`acme.app…` → Acme workspace).

Every other feature assumes `tenantId`, `workspaceId`, and permissions from session—not a global company picker on a shared host.

### Company profile & culture (for jobs and AI)

Workspace settings include **employer context** reused across job creation and candidate analysis:

| Area | Examples | Used by |
|------|----------|---------|
| **Public company blurb** | About us, industry, size, HQ | Default **about company** on new jobs ([jd-builder.md](./jd-builder.md)); overridable per job |
| **Culture & values** | Values, working style, collaboration norms, “fit” descriptors | AI JD tone; **candidate fit / culture alignment** summaries on profiles |
| **Defaults** | Timezone, default work mode, compensation visibility policy | Pre-fill new job posting fields |
| **AI analyzers** | Enable/disable catalog; clone Hyreefy defaults | [ai-skill-analyzers.md](./ai-skill-analyzers.md) settings area |

Managers with settings permission edit these once; hiring managers see inherited context when creating jobs. Server stores culture profile as structured fields (and optional long-form text) passed to orchestration APIs—not embedded only in client prompts.

## Plan

1. **Dependencies** — [auth.md](./auth.md) + [rbac.md](./rbac.md) (tenant-scoped login, JWT, permission keys, `tenants` / `workspaces` / membership schema) before settings UI.
2. **Phase A** — Session + `WorkspaceProvider` expose current tenant/workspace to domains (permissions already in session from RBAC).
3. **Phase B** — Settings UI: company name, timezone, **about company** default, **culture & values** profile, default posting defaults (work mode, comp visibility), default pipeline template (scoped to current tenant workspace).
4. **Phase C** — Member list, invite flow (links must use tenant subdomain URL), role assignment (UI mirrors backend RBAC).
5. **Phase D** — **Analyzers** settings route: gallery, clone defaults, publish workspace analyzers (manager).
6. **Provisioning (ops / later product)** — Creating a new customer = new tenant slug + workspace + seed roles; not self-service signup in v1.

### Sprint 2 delivery scope

| Phase | In sprint 2 | Notes |
|-------|-------------|--------|
| A | Yes | Provider + hooks + fetchers |
| B | Yes | Settings UI (mockup-aligned sections) |
| C | Yes | Members, invite link, role change |
| D | No | Analyzer settings → [ai-skill-analyzers.md](./ai-skill-analyzers.md) |
| Provisioning | No | Ops/seed only in v1 |

## Dev

### Contract

| Area | Details |
|------|---------|
| Workspace read | `GET /api/v1/workspaces/current` — profile, culture, posting defaults for session `workspaceId` |
| Workspace patch | `PATCH /api/v1/workspaces/:id` — settings sections; requires `settings.manage`; `:id` must match JWT `workspaceId` |
| Members | `GET/POST/PATCH /api/v1/workspaces/:id/members` — paginated list, invite, role change; mirrors RBAC tables |
| Scope | All routes JWT + `workspaceId`; reject cross-tenant workspace ids |

**Members list:** `GET` supports `page` + `limit` (default 50, max 100). **Invite v1:** persist `workspace_members` with `status: invited` and return tenant-subdomain invite URL; outbound email is [emails.md](./emails.md), not required for this slice.

### Client

- `src/domains/workspace/` — types, `api/`, `hooks/useWorkspace`, `hooks/useMembers`
- `WorkspaceProvider` in `app-providers.tsx` (session tenant/workspace)
- `(dashboard)/settings/...` thin pages; RHF + Zod per section

### Server

- `server/src/domains/workspaces/` (planned) — settings CRUD, member invite; `PermissionsGuard` on mutating routes
- Tenant FK on every row; filter `deleted_at IS NULL`

### Data

`workspaces` is widened in `server/drizzle/0002_foundation_schema_at_scale.sql`. Settings routes are not built yet; these columns are **stored now, API later**. Login still returns only workspace `id` and `name`.

| Column | Notes |
|--------|--------|
| `id` | uuid PK |
| `tenant_id` | FK → `tenants.id`; unique in v1 (one primary workspace) |
| `name` | |
| `about_company` | public about-us blurb for new jobs |
| `industry` | |
| `company_size` | |
| `headquarters` | |
| `culture_values` | jsonb string list |
| `working_style` | |
| `collaboration_norms` | |
| `fit_descriptors` | |
| `culture_long_form` | optional long-form culture text |
| `timezone` | |
| `week_starts_on` | `monday` \| `sunday`; General settings ([SettingsGeneral.html](../../designs/screens/SettingsGeneral.html)). Stored now, API later |
| `date_format` | display preference enum; General settings. Stored now, API later |
| `logo_url` | nullable; mockup shows upload — stored now, API later (upload pipeline non-goal until spec expands) |
| `default_work_mode` | posting default |
| `compensation_visibility` | posting default |
| `created_at`, `updated_at` | |
| `created_by`, `updated_by` | nullable FK → `users.id` |
| `deleted_at` | soft delete; login ignores deleted workspaces |

`default_pipeline_template_id` is not stored. It needs the workflows table, which is a new entity and is not created in this migration. Branding asset URL is not stored; the spec names “branding basics” without a column. Member columns (`status`, `invited_by`) are on `workspace_members` in [rbac.md](./rbac.md).

### Tests

- **Server unit + API:** workspace scope, member invite, 403 without `settings.manage`
- **Client unit:** workspace hooks, settings form schemas
- **Client e2e:** Settings edit on `acme.localhost` vs `beta.localhost` isolation when APIs land

### Production readiness (slice)

- JWT + `PermissionsGuard` on all mutating routes; Zod on PATCH/invite bodies; set `updated_by` on writes; lists respect `deleted_at IS NULL`.
- No client-only permission for settings edits; consistent API errors (no stack traces).
- Deploy unchanged: [deployment.md](../deployment.md).

### Performance & scale (v1)

- One primary workspace row per tenant — `GET current` is a single-row read; client may cache with TanStack Query (`staleTime` ~60s, invalidate on PATCH).
- Member directory paginated; avoid unbounded member payloads.
- Culture/profile fields stay on `workspaces`; do not load analyzer or integration tables on general settings pages.

## Non-goals

- Self-service signup and tenant provisioning UI
- Billing checkout, invoices, and payment method UI (`billing.manage` may exist in catalog for later)
- Logo/branding **upload** pipeline (column `logo_url` may exist; mockup UI can show placeholder until storage ships)
- Default pipeline template selection (requires workflows entity)
- Analyzer gallery and publish (Phase D)
- Transactional invite email in STEP 3 (link + membership row only)

## Acceptance criteria

- [ ] Session exposes tenant + workspace + permissions to hooks
- [ ] Settings pages edit only the workspace for the current subdomain
- [ ] Invites and role changes reflected in `<Can>` behavior after session refresh policy
- [x] Company about + culture profile persisted and returned on workspace API for JD builder and AI analysis
- [x] General settings `week_starts_on`, `date_format`, `logo_url` stored and exposed on GET/PATCH (upload pipeline still deferred)
- [ ] All routes and API handlers scoped by workspace server-side; no cross-tenant reads

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/SettingsGeneral.html` | General workspace settings |
| `designs/screens/SettingsCompany.html` | Company profile |
| `designs/screens/SettingsDefaults.html` | Job defaults |
| `designs/screens/SettingsCulture.html` | Culture & values, unsaved changes |
| `designs/screens/SettingsCultureReadOnly.html` | Culture read-only role |
| `designs/screens/SettingsMembers.html` | Members list |
| `designs/screens/SettingsInvite.html` | Invite flow |
| `designs/screens/SettingsRoles.html` | Roles (see [rbac.md](./rbac.md)) |

## UI fidelity (canonical mockups)

Settings uses the **rail + content** pattern from `SettingsGeneral.html`. Implement these routes under `(dashboard)/settings/…`:

| Rail item (mockup) | Screen | Sprint / feature |
|--------------------|--------|------------------|
| General | `SettingsGeneral.html` | Sprint 2 — workspace name, subdomain display (read-only), logo, timezone, week start, date format |
| Company profile | `SettingsCompany.html` | Sprint 2 — `about_company`, industry, size, HQ |
| Culture & values | `SettingsCulture.html`, `SettingsCultureReadOnly.html` | Sprint 2 — culture jsonb fields; read-only variant for Recruiter |
| Job defaults | `SettingsDefaults.html` | Sprint 2 — work mode, comp visibility defaults |
| Members | `SettingsMembers.html`, `SettingsInvite.html` | Sprint 2 |
| Roles & permissions | `SettingsRoles.html` | [rbac.md](./rbac.md) |
| Integrations | `SettingsIntegrations.html` | [external-integrations.md](./external-integrations.md) |
| Pipeline templates, AI analyzers, Email sending, Billing | `#` in mockup | Later features — hide or disabled until shipped |

Unsaved changes banner: `SettingsCulture.html`. Save footer pattern: disabled **Save changes** until dirty.

## References

- [multi-tenancy.md](../architecture/multi-tenancy.md)
- [permissions.md](../architecture/permissions.md)
- [permissions-audit.md](./permissions-audit.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
