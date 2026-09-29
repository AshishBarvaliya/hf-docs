# Analyzers domain (planned)

**Code:** `src/domains/analyzers/`

## Purpose

Manager settings for analyzer catalog, job-level override picker, and candidate **Analysis** report UI.

## Surfaces

- `(dashboard)/settings/analyzers` — gallery, editor, publish
- Job settings — assigned analyzer + auto-match preview
- Candidate profile **Analysis** tab — report list and detail
- Interview list — analysis status badge

## Hooks (target)

- `useAnalyzers()`, `useAnalyzerMutations()`
- `useCandidateAnalysisReports(candidateId, jobId?)`
- `useJobAnalyzerAssignment(jobId)`

## References

- [ai-skill-analyzers.md](../../features/ai-skill-analyzers.md)
- [workspace.md](../../features/workspace.md)
- [candidate-profile.md](../../features/candidate-profile.md)

Roadmap **STEP 12** (with profile/interview surfaces from 6–9).
