# Candidates domain (server)

**Client spec:** [`../../features/candidates-ats.md`](../../features/candidates-ats.md)  
**Module:** `src/domains/candidates/`

## Current scope

- `GET /api/v1/candidates` — bearer JWT and `candidates.read`. Workspace-scoped application rows (when `candidate_applications` exist) with cursor pagination, `jobId`, `search`, and `sort`. Legacy fallback lists `candidates` rows when no applications are seeded.
- `GET /api/v1/candidates/:candidateId` — bearer JWT and `candidates.read`. Workspace-scoped candidate header (`name`, `role`, `stage`, `resumeUrl`, disposition when set) plus `applications[]` (list-item shape + `disposition`, `dispositionReason`). Wrong workspace or soft-deleted → **404**.
- `GET /api/v1/candidates/:candidateId/activity` — bearer JWT and `candidates.read`. Paginated timeline (`limit`, `cursor`) from `candidate_activity`; ordered by `occurred_at` desc. Wrong workspace → **404**.
- `PATCH /api/v1/candidates/:id/stage` — bearer JWT and `candidates.edit`. `:id` is `candidate_applications.id`. Body `{ stage: string }`. Updates `candidate_applications.stage` in the caller’s workspace; returns the same list-item shape as GET. Missing application or other workspace → **404**.
- `PATCH /api/v1/candidates/:applicationId/reject` — bearer JWT and `candidates.reject`. Body `{ reasonCode, internalNote? }`. Sets application (and candidate) disposition `rejected`, stage `Rejected`; returns application detail shape.
- Missing or invalid token → **401**. Authenticated without the required permission → **403**.

## Tables

`candidates`, `candidate_applications`, and `candidate_activity` (see [candidate-profile.md](../../features/candidate-profile.md) **Dev → Data**).

## Next

Workflow-backed stage validation, bulk actions, screening override, and profile tabs that depend on steps 8–12 (Assessments, Emails, Analysis). Phase A + Phase B (detail, reject, resume, activity) shipped — see [candidate-profile.md](../../features/candidate-profile.md).
