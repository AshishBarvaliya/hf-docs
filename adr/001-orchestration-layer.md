# ADR 001: Hiring OS as orchestration layer

**Status:** Accepted  
**Date:** 2026-09-28

## Context

Meeting, coding assessment, proctoring, and related capabilities are **separate products/backends**. A single monolithic frontend would duplicate complex UIs and blur ownership.

## Decision

The client app is the **Hiring OS**: system of record and orchestration UI for recruiters. It integrates specialized products through APIs. The client configures workflows, assigns assessments, schedules meetings, and displays status—it does not reimplement those runtimes.

## Consequences

- Domain `api/` layers per integration; normalized hooks for UI.
- Workflow stage transitions are server-authoritative.
- Docs and roadmap prioritize dashboard, jobs, ATS, pipeline, then integrations (assessment, meeting, email).
- E2E tests focus on orchestration flows, not assessment internals.

## References

- [architecture/overview.md](../architecture/overview.md)
- [architecture/integrations.md](../architecture/integrations.md)
