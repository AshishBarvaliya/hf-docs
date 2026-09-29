# ATS domain

**Code:** `src/domains/ats/`

## Purpose

Shared ATS concepts that span candidates, jobs, and pipeline: stage enums, shared DTO mappers, cross-cutting types—not every ATS screen lives here.

## Guidelines

- Keep **feature UI** in `candidates`, `jobs`, `pipeline` domains.
- Use `ats` for types and helpers reused by 2+ domains (e.g. `PipelineStage`, normalized stage labels).
- Avoid turning `ats` into a junk drawer; promote to `shared/` only for non-ATS-generic utilities.

## Status

Scaffold (`types.ts`, barrel) — expand as pipeline and workflow land.
