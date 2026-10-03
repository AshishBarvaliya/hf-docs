# Async jobs domain (planned)

**Code (target):** `server/src/domains/async-jobs/` and/or `server/src/workers/` (worker entrypoint separate from Nest HTTP bootstrap).

## Purpose

**Authoritative** job lifecycle for queued work: enqueue from domain services, status for clients, audit trail. **Does not** execute Bedrock calls in HTTP handlers for long-running types (ADR [006](../../adr/006-async-ai-workers.md)).

## Responsibilities

| Area | Behavior |
|------|----------|
| **Create** | Insert job row `queued`; send SQS message with `jobId`, `type`, `workspaceId` |
| **Read** | `GET /api/v1/async-jobs/:id` — status, progress hint, result refs; permission check matches owning resource |
| **Cancel** | Optional v1: mark `cancelled` if still `queued`; worker no-ops if already `running` |
| **Idempotency** | Accept `Idempotency-Key` header or domain-specific dedupe keys |
| **Callbacks** | Domain services (analyzers, jobs) attach `outputRef` on completion via worker writing through shared service layer |

## API sketch

| Method | Path | Permission |
|--------|------|------------|
| GET | `/api/v1/async-jobs/:jobId` | Resource-scoped (e.g. job editor, candidate read) |
| POST | `/api/v1/async-jobs` | Internal or typed creates from domain controllers |

Prefer **domain-facing** endpoints where clearer, e.g. `POST /api/v1/jobs/:jobId/jd/parse` that returns `{ jobId }`—still backed by the same job table.

## Worker contract

- Poll SQS (long polling).
- Load job; transition `running`; fetch inputs from DB/S3 with workspace scope.
- Invoke processors by `type` registry (`JdParseProcessor`, `AnalyzerRunProcessor`, …).
- On success: write outputs, `succeeded`; delete message.
- On retryable failure: throw to leave message for retry; on terminal failure: `failed`, DLQ after max attempts.

## Schema (conceptual)

Table `async_jobs` (name TBD in migration): fields per [async-jobs.md](../../architecture/async-jobs.md).

## References

- [architecture/async-jobs.md](../../architecture/async-jobs.md)
- [analyzers/README.md](../analyzers/README.md)
- [features/jd-builder.md](../../features/jd-builder.md)
