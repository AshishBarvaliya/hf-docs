# Workspace & company

**Routes:** `(dashboard)/settings`, onboarding flows, member invites  
**Domain:** `workspace` (to implement)  
**Roadmap step:** 3  
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

## Dev

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/workspace/` — types, `api/`, `hooks/useWorkspace`, `hooks/useMembers` |
| Providers | Extend `app-providers.tsx` with `WorkspaceProvider` (reads session tenant/workspace) |
| Routes | `src/app/(dashboard)/settings/...` thin pages |
| lib | `src/lib/auth/` session helpers; `src/lib/tenant/` for host context |
| Server | `workspaces` belong to `tenants`; all settings APIs filter by authenticated workspace |

- **Forms:** RHF + Zod for settings sections; optimistic updates only for low-risk fields.
- **Tests:** Unit tests for permission map reducer; E2E on two subdomains (`acme`, `beta`) when auth harness exists.

## Acceptance criteria

- [ ] Session exposes tenant + workspace + permissions to hooks
- [ ] Settings pages edit only the workspace for the current subdomain
- [ ] Invites and role changes reflected in `<Can>` behavior after session refresh policy
- [ ] Company about + culture profile persisted and returned on workspace API for JD builder and AI analysis
- [ ] All routes and API handlers scoped by workspace server-side; no cross-tenant reads

## References

- [multi-tenancy.md](../architecture/multi-tenancy.md)
- [permissions.md](../architecture/permissions.md)
- [permissions-audit.md](./permissions-audit.md)
