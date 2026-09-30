# AI UX principles

AI is **embedded in workflows**, not a separate sidebar product.

## Patterns

| Surface | Example actions |
|---------|-----------------|
| Job / JD builder | Paste JD → parse; generate full JD; improve description; extract skills from text; inclusive language |
| Workflow / screening | Suggest criteria and default cutoffs from JD skills/requirements; admin edits before publish |
| Candidate profile | Summarize candidate; skill gaps vs job; **culture / values fit** using workspace culture + job context |
| Skill analyzers | Post-interview reports from recordings; weighted by job skill **contribution**; pre-interview profile mode |
| Settings / analyzers | Managers edit rubrics, report templates, enable tech × seniority profiles |
| Interview | Summarize interview notes |
| Pipeline | Explain drop-off between stages |
| Search | Natural language filters (“backend, 5+ years Node, assessment complete”) |

Use `components/ai/` for consistent affordances (sparkle icon, `AIAction` menu, loading/disclaimer).

## Trust and labeling

- **AI-generated content must be visually distinct** from verified candidate-submitted data.
- Show source when available (model, timestamp, “suggested — not verified”).
- Destructive or compliance-sensitive actions still require human confirmation and server validation.

## Implementation

- AI calls go through domain `api/` (streaming optional later); never expose API keys client-side.
- **Long-running work** (JD parse/generate, skill extraction, analyzer reports, exports) is **async**: API returns a `jobId`; client polls job status. Execution runs on **standalone SQS workers** with Bedrock ([async-jobs.md](./async-jobs.md), ADR [006](../adr/006-async-ai-workers.md)).
- Prefer inline actions over a single global chat unless the user opens “assistant” explicitly.

No dedicated “AI” top-level nav item; AI appears in context on jobs, candidates, pipeline, and search.
