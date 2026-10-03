# ADR 006 — Standalone queue workers for AI and reports

## Status

Accepted — 2026-09-30

## Context

Hyreefy uses AI for JD parsing/generation, skill extraction, candidate summaries, and **skill analyzer reports** from interview recordings. These workloads are **slow**, **retriable**, and **bursty**. Running them inside the NestJS HTTP process would risk timeouts, uneven load, and poor isolation when Bedrock or downstream integrations fail.

The platform runs on AWS (ADR 005). We need a clear boundary: the orchestration API accepts work, persists intent, and returns quickly; **prompt processing and report generation** run in **standalone workers** fed by a **dedicated queue**.

## Decision

- **Pattern:** Command/query through the API; **execution on workers**.
  1. Authenticated API (or internal webhook) validates permissions, writes a **job record** (Postgres), enqueues a message to **Amazon SQS**.
  2. **Standalone worker service(s)** (separate deployable from the Nest API) poll SQS, load job context from Postgres/S3, call **Amazon Bedrock** (and other integrations), write results, update job status.
  3. Client polls job status or receives updates via existing query refetch (WebSocket/SSE optional later).

- **Queue topology (v1):**
  - At minimum: one **primary AI work queue** + **dead-letter queue (DLQ)**.
  - Optional **priority** or **job-type** queues later (e.g. `hyreefy-ai-interactive` vs `hyreefy-ai-reports`) without changing the API contract.
  - **FIFO** only where strict ordering per candidate-job is required; default **standard** queues for throughput.

- **Job types** (extensible enum in DB + message payload):

| Type | Examples | Typical trigger |
|------|----------|-----------------|
| `jd.parse` | Paste JD → structured fields | JD builder |
| `jd.generate` | Prompt → draft JD | JD builder |
| `jd.extract_skills` | Description → skill matrix | JD builder |
| `candidate.summarize` | Resume + job context | Candidate profile |
| `analyzer.run` | Recording/transcript → report | Meetings webhook, manual re-run |
| `report.export` | Analysis → PDF/blob (later) | Analytics / profile |

- **Workers are not the system of record.** Postgres holds job state (`queued` → `running` → `succeeded` | `failed`), workspace scoping, and output references. SQS is for delivery and retry only.
- **Idempotency:** Messages carry `jobId` (and optional dedupe key per interview session + analyzer version). Workers must tolerate at-least-once delivery.
- **Security:** Workers use IAM roles (no long-lived keys); Bedrock and S3 access via task role. Messages contain **IDs**, not full PII blobs—workers load scoped rows under `workspace_id` / `tenant_id` checks equivalent to API guards.
- **Failure:** Exponential backoff via SQS visibility timeout; after max receives, message lands in DLQ; job row marked `failed` with safe error summary for UI; no stack traces to clients.

- **Deployable:** Worker processes run as **ECS Fargate service** (or separate worker repo) scaled on queue depth (CloudWatch + Application Auto Scaling). Nest API does **not** embed Bedrock calls for these job types in request handlers.

- **Synchronous exception:** Trivial validation or sub-second reads may stay in API; any call that may exceed **~3s** or invoke Bedrock for generation **must** use the job queue.

## Consequences

- Feature specs (JD builder, analyzers, candidate AI) document **async job IDs** and UI loading states (`pending`, `running`, `ready`, `failed`).
- Server domains expose domain-specific enqueue endpoints that return `{ jobId, status }` and poll **`GET /api/v1/async-jobs/:id`** for status/result (not `GET /api/v1/jobs/:id`, which is job CRUD).
- E2E tests stub workers or run a local worker in CI for one golden path per job type.
- Observability: structured logs with `jobId`, `workspaceId`, `jobType`; metrics on queue age and failure rate.
- Local dev: `npm run worker:dev` (or docker compose service) sharing DB with API; optional ElasticMQ/LocalStack for SQS.

## Non-goals (v1)

- Step Functions for every job (use when multi-step sagas exceed simple retry semantics).
- Client-side or browser-direct Bedrock calls.
- Blocking HTTP “wait until report ready” for analyzer runs (always async with poll).

## Alternatives considered

- **In-process Nest `@nestjs/bull` only** — rejected; same deploy as API blurs scaling and failure domains; still acceptable locally as adapter to same job table.
- **Lambda per message** — viable for some job types; default **Fargate workers** for longer reports and consistent Bedrock connection patterns; may split hot paths later.
- **EventBridge only (no SQS)** — rejected as primary; EventBridge useful for schedules and fan-out, SQS for worker pull and backpressure.

## References

- [architecture/async-jobs.md](../architecture/async-jobs.md)
- [domains/server/async-jobs/README.md](../domains/server/async-jobs/README.md)
- [005-aws-platform.md](./005-aws-platform.md)
- [ai-skill-analyzers.md](../features/ai-skill-analyzers.md)
- [jd-builder.md](../features/jd-builder.md)
- [ai-ux.md](../architecture/ai-ux.md)
