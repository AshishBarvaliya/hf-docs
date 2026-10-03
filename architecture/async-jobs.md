# Async jobs (AI prompts, reports, exports)

Long-running **prompt processing**, **JD AI**, and **analyzer report generation** run outside the Nest HTTP process on **standalone workers** backed by **Amazon SQS** (ADR [006](../adr/006-async-ai-workers.md), platform [aws-platform.md](./aws-platform.md)).

## Flow

```text
Client                    API (Nest)                    Worker (Fargate)
  │                          │                                │
  │ POST enqueue             │                                │
  ├─────────────────────────►│ create job row (queued)        │
  │                          │ SendMessage → SQS              │
  │◄─────────────────────────┤ { jobId, status: queued }      │
  │                          │                                │
  │ GET job / poll Query     │                                │ ReceiveMessage
  ├─────────────────────────►│                                ├──────────────►
  │                          │                                │ load job + workspace scope
  │                          │                                │ Bedrock + S3 + DB update
  │                          │                                │ status → succeeded | failed
  │◄─────────────────────────┤ read job row + result refs     │
  │ render UI                │                                │
```

## Job record (conceptual)

Stored in PostgreSQL (see [domains/server/async-jobs/README.md](../domains/server/async-jobs/README.md)):

| Field | Purpose |
|-------|---------|
| `id` | Public job id returned to client |
| `workspaceId` / `tenantId` | Scoping; worker re-validates |
| `type` | e.g. `jd.parse`, `analyzer.run` |
| `status` | `queued` \| `running` \| `succeeded` \| `failed` \| `cancelled` |
| `inputRef` | JSON pointers (job id, candidate id, analyzer id)—not full resume text in queue |
| `outputRef` | Result id, S3 key, or embedded summary JSON |
| `error` | Safe user-facing message when failed |
| `createdBy` | User id for audit |
| `idempotencyKey` | Optional; e.g. interview session + analyzer version |

## Message payload (SQS)

Minimal JSON:

```json
{
  "jobId": "uuid",
  "type": "analyzer.run",
  "workspaceId": "uuid",
  "attempt": 1
}
```

Workers delete the message only after successful persistence of terminal state.

## Job types and product surfaces

| Type | User surface | Result |
|------|--------------|--------|
| `jd.parse` | JD builder — paste | Structured draft fields |
| `jd.generate` | JD builder — AI | Suggested title, description, skills |
| `jd.extract_skills` | JD builder | Skill chips + priority suggestions |
| `candidate.summarize` | Candidate profile | Fit summary (AI-labeled) |
| `analyzer.run` | Profile / interview row | [Analysis report](../features/ai-skill-analyzers.md) |
| `report.export` | Analytics (later) | S3 object + download link |

## UI contract

- Show **in progress** states on the triggering surface (JD step spinner, interview row “Analysis running…”, profile tab).
- Use TanStack Query to poll **`GET /api/v1/async-jobs/:id`** or domain-specific “latest job for resource” endpoints until terminal state.
- Follow [ai-ux.md](./ai-ux.md): label outputs as AI-generated; show failure with retry when permitted.

## Retries and DLQ

- SQS visibility timeout tuned to p95 job duration.
- After max receives → DLQ; alert via CloudWatch; job `failed` in DB.
- Managers may **re-run** analyzer jobs (new job row + new message); old reports retained.

## Local development

- API enqueues to a dev queue URL or in-memory adapter writing the same job table.
- Run worker process alongside `npm run start:dev` in `server/` (or `workers/` package when added).
- Bedrock: use staging credentials or mock provider behind `AI_PROVIDER=mock` in dev.

## References

- [domains/server/async-jobs/README.md](../domains/server/async-jobs/README.md)
- [domains/server/analyzers/README.md](../domains/server/analyzers/README.md)
- [ai-ux.md](./ai-ux.md)
