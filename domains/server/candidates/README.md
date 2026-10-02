# Candidates domain (server)

**Client spec:** [`../../features/candidates-ats.md`](../../features/candidates-ats.md)  
**Module:** `src/domains/candidates/`

## Current scope

- `GET /api/v1/candidates` — bearer JWT and `candidates.read`. Lists candidates for `workspaceId` on the token, ordered by name, where `deleted_at` is null.
- Response shape matches client `candidateSchema` (`id`, `name`, `role`, `stage`). Other columns are stored and omitted from this JSON.
- Missing or invalid token → **401**. Authenticated without `candidates.read` → **403**.

## Table

`candidates` after `drizzle/0002_foundation_schema_at_scale.sql` (full list in [candidates-ats.md](../../features/candidates-ats.md) and [candidate-profile.md](../../features/candidate-profile.md)):

`id`, `workspace_id`, `name`, `role`, `stage`, `source`, `resume_url`, `disposition`, `disposition_reason`, `screening_snapshot`, `created_at`, `updated_at`, `created_by`, `updated_by`, `deleted_at`.

`source`, resume, and screening fields are stored now, API later. Scores, experience, and per-job applications are not on this table.

## Next (from client feature spec)

- Filters, sort, pagination/cursor.
- Job-scoped lists.
- `POST /workflow/move-stage` (workflow module).
- Server-side authorization per [`../../architecture/permissions.md`](../../architecture/permissions.md).
