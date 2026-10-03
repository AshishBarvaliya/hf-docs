# ADR 007 — Hosting phases (Render/Neon now, AWS target)

## Status

Accepted — 2026-10-05

## Context

ADR [005](./005-aws-platform.md) states AWS as the long-term primary cloud. Production currently runs on Render + Neon + Vercel ([deployment.md](../deployment.md)).

## Decision

- **Phase 0 (now):** Ship on Render/Neon/Vercel. Async AI may use Postgres job rows; workers optional in dev; no SQS requirement in phase 0 prod.
- **Phase 1:** Migrate API and workers to AWS per [aws-platform.md](../architecture/aws-platform.md) with a written cutover checklist (DNS, CORS, secrets, RDS, SQS, Bedrock).
- **Documentation:** Feature specs must not assume SQS/Bedrock in phase 0 unless behind a feature flag or non-goal.

## Consequences

- [operational-readiness.md](../architecture/operational-readiness.md) is the runbook index.
- Implementation task [2026-09-30-aws-sqs-ai-workers.md](../planning/implementation-tasks/2026-09-30-aws-sqs-ai-workers.md) remains **proposed** until phase 1 kickoff.
