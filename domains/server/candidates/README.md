# Candidates domain (server)

**Client spec:** [`../../features/candidates-ats.md`](../../features/candidates-ats.md)  
**Module:** `src/domains/candidates/`

## Current scope

- `GET /api/v1/candidates` — bearer JWT and `candidates.read`. Lists candidates for `workspaceId` on the token, ordered by name.
- Response shape matches client `candidateSchema` (`id`, `name`, `role`, `stage`).
- Missing or invalid token → **401**. Authenticated without `candidates.read` → **403**.

## Next (from client feature spec)

- Filters, sort, pagination/cursor.
- Job-scoped lists.
- `POST /workflow/move-stage` (workflow module).
- Server-side authorization per [`../../architecture/permissions.md`](../../architecture/permissions.md).
