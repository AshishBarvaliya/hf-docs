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

| Product | Domain | Hooks (target) |
|---------|--------|----------------|
| ATS / workflow | `candidates`, `pipeline`, `jobs` | existing + workflow mutations |
| Assessment | `assessments` | `useAssessment`, `useAssessments`, assign mutation |
| Meetings | `meetings` | `useInterview`, `useInterviews`, schedule mutation |
| Email | `emails` | `useEmailThread`, `useSendEmail` |
| Analytics | `analytics` | `useAnalytics`, `useJobAnalytics` |

- **Rule:** No product hostnames in components ([integrations-layer.mdc](../../.cursor/rules/integrations-layer.mdc)).
- **Errors:** Surface per tab with retry; block actions when integration unavailable.
- **Tests:** Mapper unit tests with fixture JSON; contract tests when OpenAPI available.

## Acceptance criteria

- [ ] All integrations env-configurable
- [ ] Normalized domain types in UI
- [ ] Deep links open external products in new tab/window
- [ ] Workflow stage transitions still server-authoritative

## References

- [architecture/integrations.md](../architecture/integrations.md)
- [interviews.md](./interviews.md)
- [emails.md](./emails.md)
- [analytics-reports.md](./analytics-reports.md)
