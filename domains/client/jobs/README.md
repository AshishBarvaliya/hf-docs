# Jobs domain (planned)

**Code:** `src/domains/jobs/` (not yet scaffolded)

## Purpose

Job list, job workspace context, **job creation wizard** (`/jobs/new`), JD builder persistence, job settings and posting metadata.

## Surfaces

- List + filters (status, owner, expiry)
- Creation: paste JD, AI generate, blank → shared `JdBuilder` schema
- Posting fields: location, compensation, about company (workspace default), expiry
- Skills/requirements: free text + normalized tags

## Hooks (target)

- `useJobs(filters)`
- `useJob(jobId)`
- `useSaveJobDraft`, `usePublishJob`, `useParseJd`, `useGenerateJd`

## References

- [jobs.md](../../features/jobs.md)
- [jd-builder.md](../../features/jd-builder.md)
- [workspace.md](../../features/workspace.md)

Scaffold this domain when starting roadmap **STEP 4**.
