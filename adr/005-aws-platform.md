# ADR 005 — AWS as primary cloud platform

## Status

Accepted — 2026-09-30

## Context

Hyreefy runs as a **shared multi-tenant** deployment (ADR 004) with a Next.js client, NestJS orchestration API, and PostgreSQL. Production needs DNS for tenant subdomains, durable storage, email, observability, and AI inference. The product direction is to run on **AWS managed services** rather than self-hosted equivalents on other clouds or raw VMs.

## Decision

- **Primary cloud:** All production Hyreefy platform infrastructure and first-party integrations default to **Amazon Web Services**.
- **Region:** One primary region per environment (e.g. `ap-south-1` or `us-east-1`); multi-region DR is out of scope until ops requires it.
- **Service map (v1 target):**

| Concern | AWS service |
|---------|-------------|
| DNS & TLS | Route 53, ACM (wildcard cert for `*.app.<domain>`) |
| Edge & static/SSR delivery | CloudFront (+ WAF) |
| Web app (Next.js) | Amplify Hosting Gen 2 **or** OpenNext on Lambda@Edge/CloudFront **or** ECS Fargate — chosen per SSR/cookie needs; must support tenant subdomains |
| API (NestJS) | Application Load Balancer + **ECS Fargate** (default) or App Runner |
| Database | **Amazon RDS PostgreSQL** or Aurora PostgreSQL (Drizzle unchanged) |
| Secrets & config | Secrets Manager, Systems Manager Parameter Store |
| Object storage | S3 (resumes, exports, branding, recording artifacts metadata) |
| Transactional email | **Amazon SES** (Hyreefy-owned sending domain) |
| Async work (AI, reports) | **SQS** queues + **standalone worker** compute (see [006-async-ai-workers.md](./006-async-ai-workers.md)) |
| Scheduling | EventBridge Scheduler → enqueue or invoke workers |
| AI inference | **Amazon Bedrock** (no client-side model keys) |
| Cache (optional) | ElastiCache Redis (rate limits, hot reads) |
| Observability | CloudWatch Logs, metrics, alarms |
| Infrastructure as code | **AWS CDK** (TypeScript), one stack family per environment |

- **Auth:** ADR 003 (Auth.js session on the client) remains valid on AWS-hosted Next.js. **Amazon Cognito** is reserved for a future enterprise SSO / IdP slice—not a silent replacement for Auth.js in v1.
- **Orchestration boundary (ADR 001):** Assessment runtime, proctoring, and live meeting rooms may be separate repos/services; when Hyreefy-owned, they also deploy on AWS (e.g. Chime SDK for meetings, dedicated ECS services for assessment). Hyreefy core still **orchestrates** via APIs and events, not inlined runtimes in the client.

## Consequences

- `.env.example` and runbooks document AWS resource names, queue URLs, and Bedrock model IDs per environment.
- CORS and cookie settings must align with CloudFront/Amplify hostnames and ADR 004 wildcard subdomains.
- Local dev continues with Docker Compose Postgres; workers can use LocalStack or a dev queue + single worker process until cloud env exists.
- Cost and quotas (Bedrock, SES, SQS) are monitored in CloudWatch; long-running AI work must not block HTTP request threads (see ADR 006).
- Engineers implement infra in CDK under a dedicated repo or `server/infra/` when implementation starts—not required for app feature slices until deploy milestone.

## Non-goals (v1)

- Multi-account AWS Organization layout (document later; start with dev/staging/prod separation via env or accounts as ops prefers).
- Customer bring-your-own-cloud (single Hyreefy SaaS on AWS).
- Using every AWS product category; choose managed services that match boundaries above.

## Alternatives considered

- **Multi-cloud / vendor-neutral K8s only** — rejected for operational focus; AWS-first matches product direction.
- **Lambda-only API** — rejected for NestJS long-lived process, connection pooling, and predictable latency for CRUD APIs.
- **Third-party email/AI** as default — rejected where Bedrock/SES meet compliance and “all AWS” direction; external models only via explicit ADR if Bedrock gaps block a feature.

## References

- [architecture/aws-platform.md](../architecture/aws-platform.md)
- [architecture/server/deployment-aws.md](../architecture/server/deployment-aws.md)
- [006-async-ai-workers.md](./006-async-ai-workers.md)
- [004-subdomain-multi-tenancy.md](./004-subdomain-multi-tenancy.md)
- [001-orchestration-layer.md](./001-orchestration-layer.md)
- [003-auth-js-session.md](./003-auth-js-session.md)
