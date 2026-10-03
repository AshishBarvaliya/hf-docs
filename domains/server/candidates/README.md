# Candidates domain (server)

**Client spec:** [`../../features/candidates-ats.md`](../../features/candidates-ats.md)  
**Module:** `src/domains/candidates/`

## Current scope

- `GET /api/v1/candidates` — bearer JWT and `candidates.read`. Workspace-scoped application rows (when `candidate_applications` exist) with cursor pagination, `jobId`, `search`, and `sort`. Legacy fallback lists `candidates` rows when no applications are seeded.
- `PATCH /api/v1/candidates/:id/stage` — bearer JWT and `candidates.edit`. `:id` is `candidate_applications.id`. Body `{ stage: string }`. Updates `candidate_applications.stage` in the caller’s workspace; returns the same list-item shape as GET. Missing application or other workspace → **404**.
- Missing or invalid token → **401**. Authenticated without the required permission → **403**.

## Tables

`candidates` and `candidate_applications` after foundation migrations (full columns in [candidates-ats.md](../../features/candidates-ats.md)).

## Next (from client feature spec)

- Workflow-backed stage validation (`POST /workflow/move-stage` or pipeline definitions).
- Bulk actions and screening override APIs.
- Server-side authorization per [`../../architecture/permissions.md`](../../architecture/permissions.md) for new mutations.
