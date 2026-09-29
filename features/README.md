# Feature specs (full product)

We are building the **complete Hyreefy client**, not a trimmed MVP. Roadmap steps describe **delivery order**, not scope cuts. Early pages (e.g. `/` widgets) are **spikes** to be replaced by the real `(dashboard)` product shell.

Delivery rules (one feature at a time, dependencies first, server + client together, no shortcuts, prod-ready): [`../.cursor/rules/product-delivery-principles.mdc`](../.cursor/rules/product-delivery-principles.mdc).

Each file below includes **Plan** (how we sequence work and what we depend on) and **Dev** (how it lands in this repo).

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

Legacy spike note: [foundation-spike.md](./foundation-spike.md).
