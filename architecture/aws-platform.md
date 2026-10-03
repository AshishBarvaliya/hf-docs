# AWS platform map

Hyreefy production runs on **AWS** (ADR [005](../adr/005-aws-platform.md)). This document is the service-level map; deploy wiring lives in [server/deployment-aws.md](./server/deployment-aws.md).

## Logical architecture

```text
                         Route 53
              (*.app.example.com + API host)
                              │
                    CloudFront (+ WAF, ACM)
                    ┌─────────┴─────────┐
                    ▼                   ▼
            Next.js (Hyreefy)     ALB → Nest API (Fargate)
            Amplify / OpenNext          │
                    │                   ├── RDS PostgreSQL (tenant/workspace data)
                    │                   ├── S3 (files, large artifacts)
                    │                   ├── SES (email)
                    │                   └── SQS (enqueue AI/report jobs)
                    │                           │
                    │                           ▼
                    │              Worker service (Fargate, standalone)
                    │                           │
                    │                           ├── Bedrock (inference)
                    │                           ├── S3 (read/write outputs)
                    │                           └── RDS (job + domain rows)
                    │
              Auth.js (ADR 003) — sessions on app host
```

**Async AI and reports** are never tied to the API request lifecycle; see [async-jobs.md](./async-jobs.md) and ADR [006](../adr/006-async-ai-workers.md).

## Service responsibilities

| AWS service | Role in Hyreefy |
|-------------|-----------------|
| **Route 53** | Hosted zone; `A`/`AAAA` or alias to CloudFront; optional API subdomain |
| **ACM** | TLS certificates (wildcard for tenant subdomains) |
| **CloudFront** | CDN, TLS termination, WAF attachment |
| **WAF** | OWASP rules, rate limiting at edge |
| **Amplify / Lambda@Edge / ECS** | Host Next.js (choice locked at deploy milestone) |
| **ALB + ECS Fargate** | Nest orchestration API |
| **ECS Fargate (workers)** | SQS consumers for Bedrock and report pipelines |
| **RDS PostgreSQL** | System of record (Drizzle) |
| **S3** | Resumes, exports, branding assets, optional report PDFs |
| **SES** | Transactional mail (invites, notifications) |
| **SQS + DLQ** | AI job delivery, retries, backpressure |
| **EventBridge Scheduler** | Cron-style enqueue (digests, retries, housekeeping) |
| **Secrets Manager / SSM** | DB URLs, Bedrock config, third-party API keys |
| **Bedrock** | JD generation/parse, summaries, analyzer prompts |
| **ElastiCache Redis** | Optional: rate limits, session cache |
| **CloudWatch** | Logs, metrics, alarms (queue depth, API 5xx, RDS) |
| **CDK** | Infrastructure definitions (TypeScript) |

## Multi-tenancy on AWS

ADR [004](../adr/004-subdomain-multi-tenancy.md) requires:

- Wildcard DNS and certificate for recruiter app hostnames.
- API CORS allowing `https://*.app.<domain>`.
- All data paths remain **workspace-scoped in Postgres** via application queries; AWS isolation is **shared app + tenant/workspace filters**, not per-tenant accounts or VPCs in v1 (Postgres RLS is optional later — [004 amendment](../adr/004-amendment-tenant-origin-binding.md)).

## External products on AWS

Per ADR [001](../adr/001-orchestration-layer.md), meeting and assessment runtimes are separate boundaries. When Hyreefy owns them:

| Product | AWS direction |
|---------|----------------|
| Meetings | Amazon Chime SDK (separate service/repo) |
| Assessments | ECS/Lambda + S3 for submissions |
| Email (orchestration) | SES from core API |

Hyreefy core integrates via APIs and webhooks; workers do not replace meeting recording storage owned by the meeting product.

## Environments

| Environment | Purpose |
|-------------|---------|
| **dev** | Developer sandboxes; may share RDS or use local Docker |
| **staging** | Full AWS shape; pre-prod integrations |
| **prod** | Customer traffic |

Secrets and queue URLs are **per environment**; never share Bedrock or SES identity across prod and staging.

## References

- [server/deployment-aws.md](./server/deployment-aws.md)
- [async-jobs.md](./async-jobs.md)
- [multi-tenancy.md](./multi-tenancy.md)
- [integrations.md](./integrations.md)
