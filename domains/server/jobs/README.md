# Jobs domain (server)

**Code:** `server/src/domains/jobs/`

## Shipped (STEP 4)

- `jobs` table — migrations `0003`, `0004` (JD/posting columns)
- `GET /api/v1/jobs` — list + filters; `jobs.read`
- `GET /api/v1/jobs/:id` — detail + header aggregates (`candidateCount`, `daysOpen`, `health`)
- `POST /api/v1/jobs` — create draft (`jobs.create`); blank / paste / AI entry (sync MVP)
- `PATCH /api/v1/jobs/:id` — draft fields (`jobs.edit`)
- `POST /api/v1/jobs/:id/publish` — Zod publish rules (`jobs.edit`)

## Planned

- Async AI parse/generate (`async_jobs`)
- Workflow template enforcement on publish

## References

- [jobs.md](../../../features/jobs.md)
- [jd-builder.md](../../../features/jd-builder.md)
