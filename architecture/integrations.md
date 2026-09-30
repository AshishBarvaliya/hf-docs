# External product integrations

Assessment, meeting, email, and analytics may run on **separate backends**. The client treats them as integration boundaries, not inlined implementations.

**Platform:** Hyreefy core and first-party workers deploy on **AWS** ([aws-platform.md](./aws-platform.md)). Transactional email from orchestration uses **Amazon SES**. Long-running AI (JD, analyzers) uses **SQS workers** ([async-jobs.md](./async-jobs.md)), not synchronous API calls.

## Layering

```text
UI (domains/*/components)
        ↓
Hooks (domains/*/hooks)     useCandidates, useJob, useAssessment, …
        ↓
API clients (domains/*/api) + shared lib/integrations config
        ↓
┌───────────┬──────────────┬────────────┬─────────────┐
│  ATS API  │ Assessment   │ Meeting    │ Email API   │ Analytics API
└───────────┴──────────────┴────────────┴─────────────┘
```

## Rules

1. **Normalized types in the domain** — map DTOs in `api/` mappers; components use domain `types.ts` only.
2. **No hostname logic in components** — base URLs from env + `lib/integrations`.
3. **Workflow side effects via backend** — e.g. moving a kanban card calls workflow/ATS API; do not mutate stage only in the browser.
4. **One hook per resource** for TanStack Query — `useCandidate(id)`, `useJobs(filters)`, consistent with existing `useCandidates`.
5. **Query keys** colocated in `api/query-keys.ts` per domain; include filter params in keys.

## Expected hooks (grow over roadmap)

| Hook | Domain | Backend |
|------|--------|---------|
| `useCandidates`, `useCandidate` | candidates | ATS |
| `useJobs`, `useJob` | jobs | ATS / jobs service |
| `usePipeline` | pipeline | ATS workflow |
| `useAssessment`, `useAssessments` | assessments | Assessment platform |
| `useInterview`, `useInterviews` | meetings | Meeting platform |
| `useAnalytics` | analytics | Analytics API |

## Failure modes

- Show domain-level error states (retry, support id); never silent failure on stage transitions.
- When an external product is down, UI may read-only cache from Query where safe; block actions that require the service.

Feature detail: [external-integrations.md](../features/external-integrations.md).
