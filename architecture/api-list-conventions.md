# API list conventions

Shared rules for workspace-scoped **list** endpoints so clients and indexes stay consistent at scale.

## Pagination

| Surface | Style | Defaults |
|---------|--------|----------|
| Admin-style lists (jobs, workspace members) | Offset `page` + `limit` | `limit` max **100** |
| High-growth feeds (candidates / applications, activity) | **Cursor** `cursor` + `limit` | `limit` max **100**, stable sort key |

Response envelope:

```json
{
  "items": [],
  "limit": 50,
  "nextCursor": "…" 
}
```

Offset lists use `page`, `limit`, `total` (see jobs list).

## Sort and filters

- Document allowed `sort` values in the feature spec; reject unknown values (400).
- Filters are **whitelisted** query params (e.g. `jobId`, `status`, `search`) — no open-ended column names.
- `search` uses indexed columns (`pg_trgm` / GIN later); document in domain README when added.

## Indexes

Every list query must use a composite index starting with `workspace_id`, matching filter + sort columns ([persistence.md](./persistence.md)).

## Async work

Long-running operations use **async jobs** at `GET /api/v1/async-jobs/:id` — not the jobs CRUD namespace ([ADR 006](../adr/006-async-ai-workers.md)).

## References

- [candidates-ats.md](../features/candidates-ats.md)
- [jobs.md](../features/jobs.md)
