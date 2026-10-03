# Operational readiness

Production today vs AWS target, plus release gates before customer-facing scale.

## Hosting phases

| Phase | Stack | Status |
|-------|--------|--------|
| **0 — Current** | Vercel (client), Render (API), Neon (Postgres) | Active — [deployment-render-neon.md](./server/deployment-render-neon.md) |
| **1 — Target** | AWS (Route 53, CloudFront, ECS, RDS, SQS, Bedrock) | Planned — [aws-platform.md](./aws-platform.md), ADR [005](../adr/005-aws-platform.md) |

ADR [007](../adr/007-hosting-phases.md) records cutover criteria (DNS, queues, SES, connection pooling). Until phase 1, async AI may use **job table + dev worker** without SQS in production.

## SLO targets (v1)

| SLI | Target | Notes |
|-----|--------|--------|
| API availability | 99.5% / 30d | Render health + `/api/v1/health` |
| API latency p95 | &lt; 500ms | Excludes async enqueue |
| Queue age (when SQS live) | p95 &lt; 5 min | AI interactive jobs |
| Error rate | &lt; 1% 5xx | Per route alarm |

## Data protection

- Neon: point-in-time recovery per provider; document RPO in runbook
- Migrations: pre-deploy on Render; single-flight locking
- **Tenant isolation:** application-level `workspace_id` filters (not Postgres RLS in v1) — [ADR 004 amendment](../adr/004-amendment-tenant-origin-binding.md)

## Release readiness review

Before marking a sprint **closed** for production-facing work:

- [ ] Tenant scope + permission tests for new routes
- [ ] List endpoints bounded (pagination/cursor)
- [ ] Public routes rate-limited
- [ ] Structured logs include `requestId` where middleware exists
- [ ] Rollback: migration reversible or forward-fix documented
- [ ] Cost: no unbounded full-table reads in request handlers

## Resilience (integrations)

- Outbound HTTP: timeouts, retries with backoff, idempotency keys on writes
- Webhooks: signature verification, DLQ for poison messages (when on AWS)
- See [external-integrations.md](../features/external-integrations.md)
