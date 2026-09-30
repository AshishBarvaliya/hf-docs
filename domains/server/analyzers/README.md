# Analyzers domain (planned)

**Code:** `server/src/domains/analyzers/`

## Purpose

**Authoritative** skill analyzer catalog, assignment to candidate-job records, async analysis jobs, and persisted reports.

## Responsibilities

| Area | Behavior |
|------|----------|
| **Defaults** | Seed Hyreefy analyzers (tech track × seniority); read-only platform rows |
| **Workspace** | Clone, draft/publish, enable/disable; rubric + report template JSON |
| **Assignment** | Match job seniority + skills + manager override → `analyzerId` on application |
| **Runs** | Create `analyzer.run` async job on recording ingest or manual re-run; worker calls Bedrock; idempotent per interview session + analyzer version ([async-jobs](../../architecture/async-jobs.md)) |
| **Reports** | Structured output: weighted scores (job contribution), per-skill, evidence segments, metadata |
| **Ingest** | Webhook from meetings domain when transcript/recording ready |

## Inputs to a run

- Published analyzer profile (workspace or default clone)
- Job skill matrix: skill, priority, contribution
- Workspace culture snapshot (optional weighting in narrative sections)
- Transcript/recording reference from meetings integration
- Candidate resume summary for profile-mode runs

## Permissions

- `analyzers.manage` — catalog edit
- `analyzers.read` — view reports
- Analysis enqueue: server-only (workflow/automation or meetings webhook)

## References

- [ai-skill-analyzers.md](../../features/ai-skill-analyzers.md)
- [interviews.md](../../features/interviews.md)
- ADR [001-orchestration-layer.md](../../adr/001-orchestration-layer.md), [006-async-ai-workers.md](../../adr/006-async-ai-workers.md)
- [async-jobs/README.md](../async-jobs/README.md)
