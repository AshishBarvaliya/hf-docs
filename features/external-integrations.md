# External integrations

**Scope:** Cross-cutting — all domains that talk to specialized backends  
**Roadmap steps:** 8 (assessment), 9 (meetings), analytics API, email service  
**Domains:** `assessments`, `meetings`, `emails`, `analytics`, plus ATS core APIs

## Product behavior

The client **orchestrates** separate products: assign and track assessments, schedule interviews, send/log emails, pull analytics. UI shows status, config, scores, links—**not** assessment IDE, proctoring, or meeting room UIs.

## Plan

1. **Integration config** — Env + `src/lib/integrations/` for base URLs, auth headers, feature flags per workspace.
2. **Phase A** — Shared fetch wrapper (trace id, error shape); DTO mappers per domain.
3. **Phase B** — Assessment domain: assign, list status, open `deepLink` to external product.
4. **Phase C** — Meeting domain: schedule, reschedule, cancel; display join links.
5. **Phase D** — Email domain: templates, send, thread list on candidate/job.
6. **Phase E** — Analytics API: funnel, job health, report exports.
7. **Dependencies** — Workspace settings for provider credentials (if per-tenant); workflow stage types from step 7.

## Dev

### Contract

| Integration | BFF / proxy pattern | Client-facing |
|-------------|---------------------|---------------|
| Assessments | `GET/POST /api/v1/assessments/*` | Normalized types in `domains/assessments/` |
| Meetings | `GET/POST /api/v1/interviews/*` | Join links only from API |
| Email | `POST /api/v1/emails/send`, thread reads | Template merge fields server-validated |
| Analytics | `GET /api/v1/analytics/*` | Same contracts as [analytics-reports.md](./analytics-reports.md) |

Orchestration calls external products; env-configured base URLs per [integrations.md](../architecture/integrations.md).

### Data

Per-integration credential rows on `workspaces` or `integration_connections` (table designed on first settings slice); no vendor hostnames in client bundle.

### Server

- `server/src/domains/integrations/` (planned) — mappers, retries, webhook ingress; JWT + workspace scope

### Client

| Product | Domain | Hooks |
|---------|--------|-------|
| ATS / workflow | `candidates`, `pipeline`, `jobs` | existing + workflow mutations |
| Assessment | `assessments` | `useAssessments`, assign mutation |
| Meetings | `meetings` | `useInterviews`, schedule mutation |
| Email | `emails` | `useEmailThread`, `useSendEmail` |
| Analytics | `analytics` | `useAnalytics`, `useJobAnalytics` |

No product hostnames in components ([integrations-layer.mdc](../../.cursor/rules/integrations-layer.mdc)).

### Tests

- **Server:** mapper unit tests with fixture JSON; contract tests when OpenAPI available
- **Client:** hook error/retry behavior; deep links open external URLs from API fields only

## Acceptance criteria

- [ ] All integrations env-configurable
- [ ] Normalized domain types in UI
- [ ] Deep links open external products in new tab/window
- [ ] Workflow stage transitions still server-authoritative

## Design mockups

| File | Notes |
|------|--------|
| `designs/screens/SettingsIntegrations.html` | Integration health (one needs attention) |
| `designs/screens/Assessments.html` | Global assessments list |
| `designs/screens/AssignAssessment.html` | Assign from candidate profile |
| `designs/screens/ProfileAssessments.html` | Assessment results on profile |
| `designs/screens/InterviewReport.html` | AI report after interview product |

## References

- [architecture/integrations.md](../architecture/integrations.md)
- [product-design-mockups.md](../architecture/product-design-mockups.md)
- [interviews.md](./interviews.md)
- [emails.md](./emails.md)
- [analytics-reports.md](./analytics-reports.md)
