# End-to-end Dev sections on all feature specs

**Status:** done  
**Created:** 2026-10-02  
**Feature spec:** N/A (process)  
**Policy:** [product-delivery-principles.mdc](../../.cursor/rules/product-delivery-principles.mdc), [features/README.md](../../features/README.md)

## Summary

Hyreefy features are built **from docs** as full vertical slices: **contract + PostgreSQL (Drizzle migrations) + Nest server + Next client + tests**. Several older `features/*.md` files document only client paths in **Dev**. Before implementing each roadmap step, extend that feature’s **Dev** with **Contract**, **Data**, **Server**, **Client**, and **Tests** subsections and ensure `sprint/client/current.yaml` and `sprint/server/current.yaml` have paired tasks.

## Out of scope (this task)

- Implementing product features themselves (only doc + sprint alignment).
- Rewriting product behavior in **Plan** sections.

## Checklist (by roadmap order)

Update **Dev** (+ domain READMEs if missing) before marking the feature in progress in sprint YAML:

- [x] [auth.md](../../features/auth.md) — add explicit **Data** table (partially in Plan; formalize migrations)
- [x] [rbac.md](../../features/rbac.md)
- [x] [overview-dashboard.md](../../features/overview-dashboard.md)
- [x] [workspace.md](../../features/workspace.md)
- [x] [jobs.md](../../features/jobs.md) + [jd-builder.md](../../features/jd-builder.md)
- [x] [candidates-ats.md](../../features/candidates-ats.md)
- [x] [candidate-profile.md](../../features/candidate-profile.md)
- [x] [hiring-workflows.md](../../features/hiring-workflows.md)
- [x] [external-integrations.md](../../features/external-integrations.md), [interviews.md](../../features/interviews.md), [emails.md](../../features/emails.md)
- [x] [automations.md](../../features/automations.md)
- [x] [ai-skill-analyzers.md](../../features/ai-skill-analyzers.md) — server/data rows (started)
- [x] [analytics-reports.md](../../features/analytics-reports.md)
- [x] [permissions-audit.md](../../features/permissions-audit.md)
- [x] [design-system-shell.md](../../features/design-system-shell.md) — client-only OK if no API

## Sprint pairing

For each feature above, when promoting to `in_progress`:

1. Add or verify server tasks: schema/migration, API, guards.
2. Add or verify client tasks: fetchers, UI, permissions UX.
3. Set the same `feature_id` / aligned `current_focus.goal` in both YAML files.

## Acceptance criteria

- [x] Every non–client-only feature spec has **Contract**, **Data**, **Server**, **Client**, **Tests** in **Dev**
- [x] `domains/server/<name>/README.md` exists for each server domain referenced (workspace README added in sprint-2 server YAML; create before workspace API slice)
- [x] No sprint feature marked `done` with open paired tasks on the sibling tracker

## Promotion to sprint

Add a docs task under `feat-delivery-process` in `sprint/client/current.yaml` when starting the first spec refresh in a session; mirror a lightweight tracking task on server if useful.
