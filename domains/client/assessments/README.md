# Assessments domain (planned)

**Code:** `src/domains/assessments/`

## Purpose

Configure and monitor assessments via **assessment platform API**—assign tests, show completion and scores, deep link to proctoring/coding product when needed.

## Hooks (target)

- `useAssessment(id)`
- `useAssessments(filters)`

## Rules

- Do not implement test runtime or proctoring UI in this client.
- Job workflow builder selects provider + template; this domain handles runtime status.

Roadmap **STEP 8**. See [external-integrations.md](../../features/external-integrations.md).
