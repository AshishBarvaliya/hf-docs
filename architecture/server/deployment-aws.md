# Server & workers — AWS deployment

Companion to [aws-platform.md](../aws-platform.md) and ADR [005](../../adr/005-aws-platform.md). Applies to **`server/`** (Nest API) and the **standalone worker** deployable (same repo package or sibling `workers/` until split).

## Components

| Component | Runtime | Notes |
|-----------|---------|--------|
| **API** | ECS Fargate behind ALB | Health: `/api/v1/health`; target group stickiness not required |
| **Workers** | ECS Fargate (separate service) | Scales on `ApproximateNumberOfMessagesVisible`; no public ALB |
| **Database** | RDS PostgreSQL | Private subnets; API and workers in same VPC |
| **Queues** | SQS + DLQ | API: `sqs:SendMessage`; workers: receive/delete |
| **AI** | Bedrock | IAM on worker task role only for inference job types |
| **Files** | S3 | Presigned upload from API; workers read/write large outputs |

## Networking

- Public: CloudFront → client; ALB → API only.
- Workers: no inbound from internet; egress to Bedrock, S3, RDS, SQS via VPC endpoints where cost-effective.
- RDS: not publicly accessible.

## Configuration

Environment variables (validated in `server/src/config/env.ts` when implemented):

| Variable | Consumer |
|----------|----------|
| `DATABASE_URL` | API, workers |
| `AWS_REGION` | API (SQS send), workers |
| `SQS_AI_QUEUE_URL` | API enqueue, workers poll |
| `SQS_AI_DLQ_URL` | Ops/alerts |
| `S3_BUCKET_*` | API presign, workers |
| `BEDROCK_MODEL_ID_*` | Workers (per job type) |

Secrets in **Secrets Manager**; non-secret ids in Parameter Store.

## CI/CD (target)

- Build container images per component (API image vs worker image may share base layer).
- Deploy API and workers **independently** so worker scaling changes do not roll the API.
- Migrations run as one-off ECS task or pipeline step before API rollout.

## Client pairing

- Next.js hosted on Amplify or CloudFront origin; `NEXT_PUBLIC_API_URL` points to API ALB or `api.<domain>`.
- Wildcard cookies and `AUTH_URL` per ADR [004](../../adr/004-subdomain-multi-tenancy.md) and [003](../../adr/003-auth-js-session.md).

## References

- [async-jobs.md](../async-jobs.md)
- [overview.md](./overview.md)
