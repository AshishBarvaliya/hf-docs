# AI skill analyzers

**Routes:** `(dashboard)/settings/analyzers`, job **JD** / **Settings**, interview round config, candidate profile **Analysis** tab  
**Domains:** `analyzers` (+ `jobs`, `meetings`, `candidates`, `workspace`)  
**Roadmap steps:** 6–9 (reports on profile), **12** (catalog + assignment); depends on meeting recordings (step 9)

## Product behavior

**Skill analyzers** are configurable AI **profiles** that evaluate candidates against a job’s structured skills—especially from **interview recordings** (transcript/audio via meeting integration)—and produce **detailed, structured reports**. Hyreefy ships many **default analyzers** differentiated by **tech domain** and **seniority**; **managers** clone, edit, enable, and assign them per workspace and job.

Analyzers do **not** make hire/reject decisions alone; they inform recruiters alongside screening rules and human judgment ([ai-ux.md](../architecture/ai-ux.md)).

### Job skills: priority & contribution

Each skill on a job (from [jd-builder.md](./jd-builder.md)) can carry scoring metadata used for assignment and report weighting:

| Attribute | Meaning | Example |
|-----------|---------|---------|
| **Skill** | Normalized name or tag | `Node.js`, `System design` |
| **Priority** | Must-have vs nice-to-have (or ordered rank) | P0 must-have, P1 important, P2 bonus |
| **Contribution** | Weight in overall fit score (0–100% across job skills, normalized on publish) | System design 40%, Node 35%, communication 25% |

Managers edit priority and contribution on the job (or accept AI-suggested defaults from JD parse). **Server** stores the published skill matrix; client displays totals and validation (contributions sum to 100% or product-defined rules).

### Analyzer catalog (defaults + workspace)

| Layer | Description |
|-------|-------------|
| **Hyreefy defaults** | Library of analyzers keyed by **tech track** (e.g. backend, frontend, mobile, data/ML, DevOps, generic) and **seniority band** (e.g. junior, mid, senior, staff/principal). Each includes rubric sections, prompt templates, and default report outline. Read-only; **clone** to workspace. |
| **Workspace analyzers** | Manager-created or cloned copies; **draft → publish**; version history optional later. |
| **Enabled set** | Managers toggle which analyzers are available for jobs in this workspace. |

Configuration surfaces (manager permission, e.g. `analyzers.manage`):

- Rubric dimensions (technical depth, problem-solving, communication, leadership for senior bands)
- Which skill priorities map to which rubric weights
- Report sections (summary, per-skill scores, evidence quotes from transcript, gaps, recommendation narrative)
- Minimum recording length / consent flags (policy hooks)

### Assignment: which analyzer runs for a candidate

When a candidate is tied to a job, the **server assigns** an analyzer (or set) using:

1. **Explicit job config** — Hiring manager picks primary analyzer on job settings or overrides auto-match.
2. **Auto-match** — From job **seniority**, primary tech tags, and skill list → best-fit default or workspace analyzer (deterministic rules documented in server domain; optional AI assist for tie-break only as suggestion).
3. **Workflow round** — **Interview** rounds in [hiring-workflows.md](./hiring-workflows.md) can pin an analyzer for that round (e.g. “Senior backend loop” → `Backend · Senior` analyzer).

Assignment is stored on the application/candidate-job record and can be **updated by manager** before or after interviews (audit when changed post-report).

```text
Job skills (priority + contribution)
        +
Job seniority / tech track
        +
Manager override (optional)
        ▼
Assigned analyzer profile(s)
        ▼
Interview completes → recording available
        ▼
Analyzer job (async) → structured report on candidate profile
```

### Interview recording analysis

After a **video/F2F interview** ([interviews.md](./interviews.md)):

1. Meeting product provides **recording URL or transcript** (webhook or poll); orchestration ingests with consent metadata.
2. Server enqueues **analyzer run** using the **assigned analyzer** + job skill matrix + workspace culture snapshot ([workspace.md](./workspace.md)).
3. Output: **Analysis report** — overall weighted score aligned to **contribution** weights, per-skill breakdown, timestamped evidence from recording, strengths/gaps, optional comparison to job must-haves (P0).

Reports are **versioned** per interview session; re-run allowed when manager updates analyzer config (new version id; old reports retained for audit).

UI on candidate profile: list reports by interview date; expand sections; clear **AI-generated** labeling; export PDF later optional ([analytics-reports.md](./analytics-reports.md)).

### Resume / pre-interview analysis (lighter path)

The same assigned analyzer can run **without recording** on resume + application data for early fit (summary only or reduced rubric). Full **detailed reports** are the primary value for **post-interview recording** analysis; spec treats both as one analyzer product with `mode: profile | interview`.

## Plan

1. **API** — Analyzer CRUD (defaults read, workspace draft/publish), job skill matrix with priority/contribution, assign analyzer to candidate-job, enqueue analysis, report CRUD, recording ingest webhook.
2. **Phase A** — Settings: analyzer gallery (defaults + workspace), clone/edit/publish, enable toggles.
3. **Phase B** — JD builder: skill priority + contribution UI; validation on publish.
4. **Phase C** — Job settings: auto-match preview + manager override picker.
5. **Phase D** — Workflow interview round: select analyzer; on complete trigger analysis when recording exists.
6. **Phase E** — Candidate profile: analysis reports list + detail; link from interview row.
7. **Phase F** — Pre-interview profile analysis (optional same analyzer, lighter report).
8. **Dependencies** — Jobs skill model (step 4); meetings + recordings (step 9); candidate profile shell (step 6); RBAC keys.

## Dev

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/analyzers/` — types, catalog hooks, report components |
| Settings | `(dashboard)/settings/analyzers` — `AnalyzerEditor`, rubric + report template forms |
| Jobs | Extend JD skills UI; job settings analyzer override |
| Meetings | Interview row status: recording pending / analysis running / report ready |
| Profile | `CandidateAnalysisReport`, evidence clip timestamps if API provides |
| AI | All runs via orchestration API; no client-side model keys |

- **Permissions:** `analyzers.manage` (settings), `analyzers.read` (recruiters), reports gated by `candidates.read`.
- **Tests:** Contribution sum validation; assignment rule unit tests; fixture report rendering.

## Acceptance criteria

- [ ] Hyreefy default analyzers by tech + seniority visible; clone and publish workspace copies
- [ ] Managers can update rubric, report sections, and enabled analyzers
- [ ] Job skills support priority and contribution; published matrix drives report weighting
- [ ] Server assigns analyzer to candidate-job (auto + override); auditable changes
- [ ] Interview recording triggers analyzer; detailed report on candidate profile
- [ ] Reports weighted by contribution; P0 gaps highlighted
- [ ] All outputs labeled AI-suggested; no automatic reject/hire from analyzer score alone

## Non-goals

- Building meeting recording infrastructure inside Hyreefy (orchestration only)
- Proctoring or live interview bots in the room
- Opaque black-box score with no evidence quotes

## References

- [jd-builder.md](./jd-builder.md)
- [workspace.md](./workspace.md)
- [hiring-workflows.md](./hiring-workflows.md)
- [interviews.md](./interviews.md)
- [candidate-profile.md](./candidate-profile.md)
- [ai-ux.md](../architecture/ai-ux.md)
- [../domains/server/analyzers/README.md](../domains/server/analyzers/README.md)
