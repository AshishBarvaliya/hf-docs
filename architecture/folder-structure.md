# Folder structure

Target layout for the Hyreefy client. **Implemented today** is partial; new work should move toward this shape without big-bang rewrites.

## App routes

```text
src/app/
├── (auth)/
│   └── …
└── (dashboard)/
    ├── layout.tsx          # shell: sidebar, header, search slot
    ├── overview/
    ├── jobs/
    │   └── [jobId]/
    │       ├── overview/
    │       ├── jd/
    │       ├── candidates/
    │       ├── pipeline/
    │       ├── assessments/
    │       ├── interviews/
    │       ├── emails/
    │       ├── automation/
    │       ├── analytics/
    │       └── settings/
    ├── candidates/
    │   └── [candidateId]/
    ├── pipeline/
    ├── assessments/
    ├── interviews/
    ├── analytics/
    ├── automations/
    ├── emails/
    └── settings/
```

Routes compose domain exports; no fetch logic or stage-transition rules in page files.

## Domains (business logic)

```text
src/domains/
├── jobs/
├── candidates/      # exists
├── pipeline/
├── assessments/
├── meetings/        # interviews; meeting product API
├── emails/
├── automations/
├── analytics/       # exists
├── ats/             # shared ATS types/cross-cutting
└── workspace/       # company, members, settings
```

Each domain follows:

```text
domains/<name>/
  types.ts
  schemas/           # Zod
  api/               # clients + query-keys
  hooks/
  components/
  index.ts           # public API
```

## Components (presentation)

```text
src/components/
├── ui/              # shadcn primitives
├── layout/          # shell, page headers
├── navigation/      # sidebar, nav items
├── hiring/          # CandidateCard, JobCard, PipelineStage, …
└── ai/              # AISummary, AIAction, …
```

Domain-specific screens live in `domains/<name>/components/` until a second domain needs the same UI; then promote to `components/hiring/` or `shared/`.

## Integration layer

HTTP clients live in **domain `api/`** folders, configured from `src/lib/integrations/` (base URLs, auth headers). UI never hard-codes product hostnames. See [integrations.md](./integrations.md).

## Docs mirror

| Code | Spec |
|------|------|
| `src/domains/<name>/` | `docs/domains/client/<name>/` |
| Major UX area | `docs/features/<name>.md` |
