# Sprint 1–2 integrity fixes

**Status:** done  
**Created:** 2026-10-03  
**Feature spec:** [overview-dashboard.md](../../features/overview-dashboard.md), [workspace.md](../../features/workspace.md), [jobs.md](../../features/jobs.md)

## Summary

Remediation slice after Sprint 1–2 audits: server-owned overview metrics, auth/RBAC hardening, workspace schema gaps, safe invites, Zod 400 responses, job owner validation, client permission gates, jobs pagination, and truthful sprint tracking before STEP 5 ATS.

## Out of scope (this task)

- Pixel-perfect settings rail and loading skeleton mockups
- Full jobs list aggregates before `candidate_applications`
- Async AI JD workers, JWT revocation, workflow publish

## Client

- [x] Overview consumes `GET /api/v1/analytics/overview` (no placeholder counts)
- [x] Tenant host session slug binding; `NoAccess` on gated routes
- [x] General settings: `weekStartsOn`, `dateFormat`, `logoUrl`
- [x] Settings/members/invite and `/jobs/new` permission gates
- [x] Jobs list pagination controls
- [x] **UI unit tests** — workspace hooks/schemas, nav settings visibility
- [x] **E2E** — live API settings save, job create → JD

## Server

- [x] `AnalyticsModule` overview endpoint
- [x] Migration `0005_workspace_general_fields.sql`
- [x] Invite conflict for active members; `ZodValidationFilter`
- [x] Job `ownerId` workspace membership validation
- [x] **API unit tests** — `jobs.service.spec.ts`, foundation columns
- [x] **API e2e** — analytics, workspace 409/400, jobs 403/400

## Acceptance criteria

- [x] No fabricated overview metrics on success path
- [x] Re-inviting active member returns 409
- [x] Invalid Zod bodies return 400
- [x] Sprint trackers opened as `sprint-integrity` with checklist

## Promotion to sprint

Delivered via `docs/sprint/client/current.yaml` and `docs/sprint/server/current.yaml` (`sprint-integrity`).
