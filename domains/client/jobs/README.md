# Jobs domain (client)

**Code:** `src/domains/jobs/`

## Purpose

Job list, job workspace, **job creation** (`/jobs/new`), JD editor on the JD tab, publish flow.

## Surfaces

- List + status tabs + search (`JobsListPanel`)
- `/jobs/new` — blank, paste, or AI entry → JD editor
- Job workspace tabs — overview + JD live; other tabs placeholder until STEP 5+

## Hooks

- `useJobs(filters)`
- `useJob(jobId)`
- `useCreateJob`, `usePatchJob`, `usePublishJob`

## API

- `GET/POST /api/v1/jobs`, `GET/PATCH /api/v1/jobs/:id`, `POST .../publish`

## References

- [jobs.md](../../features/jobs.md)
- [jd-builder.md](../../features/jd-builder.md)

**Routes:** `(dashboard)/jobs`, `(dashboard)/jobs/new`, `(dashboard)/jobs/[jobId]/*`.
