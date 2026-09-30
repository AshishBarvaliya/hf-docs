# AWS platform + SQS AI workers

**Status:** proposed  
**Created:** 2026-09-30  
**Feature spec:** [features/ai-skill-analyzers.md](../../features/ai-skill-analyzers.md), [features/jd-builder.md](../../features/jd-builder.md)  
**ADRs:** [005-aws-platform.md](../../adr/005-aws-platform.md), [006-async-ai-workers.md](../../adr/006-async-ai-workers.md)

## Summary

Implement production AWS hosting (CDK baseline) and the **async job pipeline**: Postgres job table, API enqueue to SQS, standalone Fargate worker consuming messages and calling Bedrock, with local dev parity (worker process + optional LocalStack).

## Out of scope (this task)

- Full Amplify vs OpenNext hosting decision implementation (document choice in deploy PR).
- Cognito / enterprise SSO.
- Step Functions sagas; PDF export (`report.export`).
- Multi-region DR.

## Client

- [ ] Shared hook `useAsyncJob(jobId)` with polling and terminal states
- [ ] JD builder: wire parse/generate/extract to job enqueue + loading UI
- [ ] Interview row + profile: analysis `running` / `failed` / `ready` from job status
- Paths: `client/src/domains/jobs/`, `client/src/domains/analyzers/`, shared `lib/async-jobs` or domain `api/`
- Tests: hook + fixture job state transitions

## Server

- [ ] Domain `async-jobs`: schema migration, service, `GET /api/v1/jobs/:id`, enqueue helper
- [ ] SQS send from API (IAM role); env validation for queue URLs
- [ ] Worker entrypoint (separate bootstrap): poll loop, processor registry by `type`
- [ ] Processors: `jd.parse`, `jd.generate`, `jd.extract_skills`, `analyzer.run` (stubs → Bedrock)
- [ ] Analyzers domain: recording webhook creates `analyzer.run` job
- [ ] Jobs domain: JD AI endpoints enqueue instead of inline inference
- Paths: `server/src/domains/async-jobs/`, `server/src/workers/`, `server/infra/` (CDK)
- Tests: idempotency, permission on job read, processor unit tests with mock Bedrock

## Contract / API

- `POST` domain endpoints return `{ jobId, status: "queued" }`
- `GET /api/v1/jobs/:jobId` → `{ id, type, status, output?, error? }` scoped by workspace
- SQS body: `{ jobId, type, workspaceId }` only

## Infra (CDK)

- [ ] VPC, RDS, ECS API service + ALB, ECS worker service (no public LB)
- [ ] SQS + DLQ, IAM roles, Secrets Manager, S3 bucket, Bedrock allow policy on worker role
- [ ] CloudWatch alarms: DLQ depth, queue age

## Acceptance criteria

- [ ] Paste JD in staging enqueues job; UI shows result after worker completes
- [ ] Analyzer run after mock recording webhook produces report row
- [ ] Failed job surfaces safe error; message eventually DLQ after retries
- [ ] API remains responsive under simulated worker slowdown (no Bedrock in request path)

## Open questions

- Single worker image vs split by job type at scale.
- Exact Next.js host on AWS (Amplify Gen 2 vs Fargate) — lock in deploy milestone PR.

## Promotion to sprint

When starting implementation, add tasks to `docs/sprint/server/current.yaml` (and client polling UI) and set status to `in_sprint`.
