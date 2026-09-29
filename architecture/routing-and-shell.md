# Dashboard shell and navigation

Recruiter shell runs on **tenant subdomains** (`https://{slug}.app.example.com`). Nav and branding are for the signed-in user’s workspace on that tenant. See [multi-tenancy.md](./multi-tenancy.md).

## Shell layout

```text
┌─────────────────────────────────────────────────────────┐
│ Logo       Search candidates/jobs...       🔔  Avatar   │
├───────────────┬─────────────────────────────────────────┤
│               │                                         │
│ Overview      │  Page content (domain-composed)         │
│ Jobs          │                                         │
│ Candidates    │                                         │
│ Pipeline      │                                         │
│ Assessments   │                                         │
│ Interviews    │                                         │
│ Analytics     │                                         │
│ Automations   │                                         │
│ Email         │                                         │
│ Settings      │                                         │
└───────────────┴─────────────────────────────────────────┘
```

Implementation targets:

- `(dashboard)/layout.tsx` — sidebar + header + `<Can>`-aware nav items
- Global search defers to a dedicated domain/API when built; placeholder is OK in early sprints
- Notifications and avatar from workspace/auth domains

## Overview (home for recruiters)

**Action-oriented**, not vanity metrics only.

Show prioritized work queues, for example:

- N candidates need review
- N interviews need scheduling
- N assessments completed (awaiting decision)
- N jobs with low candidate flow

Supplement with summary stats (open jobs, applied count, interviews scheduled) and a hiring funnel snippet plus recent candidates. Spec: [overview-dashboard.md](../features/overview-dashboard.md).

## Navigation rules

- Nav labels and routes stay in sync with `docs/features/` and i18n keys under `messages/`.
- Hide or disable items the user lacks permission for (see [permissions.md](./permissions.md)).
- Job-scoped sub-nav lives under `/jobs/[jobId]/…` tabs, not top-level sidebar clutter.
