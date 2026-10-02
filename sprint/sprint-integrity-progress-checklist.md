# Sprint integrity — progress checklist

**Sprint id:** `sprint-integrity` · **Dates:** 2026-10-03 → 2026-10-11  
**YAML %:** Client **100%** · Server **100%** (scoped remediation tasks only)

**Task doc:** [sprint-integrity-fixes](../planning/implementation-tasks/2026-10-03-sprint-integrity-fixes.md)

---

## Client

- [x] Overview API wiring (no hardcoded metrics)
- [x] Permission page gates (jobs create, settings members/invite)
- [x] Culture reachable via settings nav without `settings.manage`
- [x] General settings fields (week start, date format, logo URL)
- [x] Jobs list pagination
- [x] Unit: overview map, workspace hooks/schemas, nav
- [x] E2E: settings save, job create → JD, live candidates RBAC

## Server

- [x] `GET /api/v1/analytics/overview`
- [x] Workspace columns migration `0005`
- [x] Safe invite (409 active member)
- [x] Global Zod → 400 filter
- [x] Job owner membership validation
- [x] Unit: jobs service, foundation columns
- [x] E2E: analytics, workspace, jobs hardening cases

## Docs

- [x] Active `current.yaml` reset to `sprint-integrity`
- [x] Roadmap status table updated
- [x] Sprint 2 client YAML % correction note (24% weighted)

| Section | Done | Total |
|---------|------|-------|
| Remediation scope | 20 | 20 |
