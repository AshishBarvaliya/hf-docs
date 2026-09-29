# AI UX principles

AI is **embedded in workflows**, not a separate sidebar product.

## Patterns

| Surface | Example actions |
|---------|-----------------|
| Job / JD builder | Improve description, generate requirements, inclusive language, screening questions |
| Candidate profile | Summarize candidate, highlight skill gaps |
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
- Prefer inline actions over a single global chat unless the user opens “assistant” explicitly.

No dedicated “AI” top-level nav item; AI appears in context on jobs, candidates, pipeline, and search.
