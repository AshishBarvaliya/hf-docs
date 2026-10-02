# Feature specs (full product)

We are building the **complete Hyreefy client**, not a trimmed MVP. Roadmap steps describe **delivery order**, not scope cuts. Early pages (e.g. `/` widgets) are **spikes** to be replaced by the real `(dashboard)` product shell.

Delivery rules (one feature at a time, dependencies first, server + client together, no shortcuts, prod-ready): [`../.cursor/rules/product-delivery-principles.mdc`](../.cursor/rules/product-delivery-principles.mdc).

Each file below includes **Plan** (how we sequence work and what we depend on) and **Dev** (how the slice lands in **client**, **server**, and **database**—not client-only).

### Dev section template (required for new/updated specs)

Every feature **Dev** section must make the full slice explicit. Use subsections or a table with at least:

| Subsection | What to document |
|------------|------------------|
| **Contract** | Routes, methods, request/response shapes, permission keys |
| **Data** | Tables/columns, FKs to tenant/workspace, migration name or domain schema path, seed data |
| **Server** | `server/src/domains/<name>/`, guards, Zod DTOs |
| **Client** | `client/src/domains/<name>/`, App Router paths, hooks, `<Can>` |
| **Tests** | **Mandatory per slice:** server **unit** (schemas, services, guards) + **API/e2e for each route**; client **unit** (hooks, helpers, permissions, forms) + **Playwright** for new routes/critical flows. List file paths or `*.spec.ts` / `*.test.tsx` patterns. |

Link **`domains/server/<name>/README.md`** and **`domains/client/<name>/README.md`** when they exist. **Plan** phase 1 should name the API; **Dev** must name persistence before the slice is marked done in sprint YAML.

Older specs may list only client paths—extend them when that feature is next on the roadmap ([implementation task](../planning/implementation-tasks/2026-10-02-e2e-feature-spec-dev-sections.md)).

| Feature | Doc | Roadmap step |
|---------|-----|--------------|
| Authentication | [auth.md](./auth.md) | 1.5 |
| RBAC | [rbac.md](./rbac.md) | 1.6 |
| Multi-tenancy (architecture) | [../architecture/multi-tenancy.md](../architecture/multi-tenancy.md) | 1.5–1.6 |
| Overview dashboard | [overview-dashboard.md](./overview-dashboard.md) | 2 |
| Workspace & company | [workspace.md](./workspace.md) | 3 |
| Jobs | [jobs.md](./jobs.md) | 4 |
| JD builder | [jd-builder.md](./jd-builder.md) | 4 |
| Candidates & ATS | [candidates-ats.md](./candidates-ats.md) | 5 |
| Candidate profile | [candidate-profile.md](./candidate-profile.md) | 6 |
| Hiring workflows | [hiring-workflows.md](./hiring-workflows.md) | 7 |
| External integrations | [external-integrations.md](./external-integrations.md) | 8–9 |
| Interviews | [interviews.md](./interviews.md) | 9 |
| Emails | [emails.md](./emails.md) | 10 |
| Automations | [automations.md](./automations.md) | 10 |
| AI skill analyzers | [ai-skill-analyzers.md](./ai-skill-analyzers.md) | 6–9, 12 |
| Analytics & reports | [analytics-reports.md](./analytics-reports.md) | 11 |
| Permissions & audit | [permissions-audit.md](./permissions-audit.md) | 13 (audit; RBAC at 1.6) |

Design system and app shell: [design-system-shell.md](./design-system-shell.md) (step 1–2).

Static screen mockups (Hyreefy `ms/` workspace): [product-design-mockups.md](../architecture/product-design-mockups.md) — each feature spec below has a **Design mockups** table mapping `designs/screens/*.html` files.

Legacy spike note: [foundation-spike.md](./foundation-spike.md).
