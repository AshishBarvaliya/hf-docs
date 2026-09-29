# JD builder & job creation

**Routes:** `(dashboard)/jobs/new`, `(dashboard)/jobs/[jobId]/jd`  
**Domain:** `jobs` (+ `workspace` for company defaults, `pipeline` / `automations` for hiring process)  
**Roadmap step:** 4

## Product behavior

**Job creation** is a guided flow (wizard or stepped form) that produces a **draft job** recruiters can publish to start hiring. The same editor powers the job **JD** tab after creation.

### How the JD content starts (three entry paths)

Recruiters and hiring managers choose one starting point; all paths land in the **same structured draft** they can edit before publish.

| Entry | UX | Outcome |
|-------|-----|---------|
| **Paste JD** | Paste plain text or rich text from doc/email | Orchestration **parses** into title, description, skills, requirements, and other detectable fields; user reviews and fixes |
| **Generate with AI** | Short prompt (role, level, team) or “generate from title” | AI drafts description + suggested skills/requirements; clearly marked **suggested** |
| **Start blank** | Manual form | User fills sections directly |

Paste and AI output never publish without review. Parsing and generation run **server-side** ([ai-ux.md](../architecture/ai-ux.md)).

### Structured job fields (posting & ops)

Beyond narrative description, every job carries **posting metadata** validated on publish (Zod on client, authoritative on server):

| Field group | Examples | Notes |
|-------------|----------|--------|
| **Basics** | Title, department, employment type (FT/PT/contract), seniority | Required for publish |
| **Location** | Work mode (remote / hybrid / onsite), city/region, relocation | Shown on job posting surfaces |
| **Compensation** | Range min/max, currency, pay period, equity flag, **visibility** (public vs internal) | Sensitive; respect visibility on external boards |
| **Company on posting** | **About company** blurb | Defaults from [workspace](./workspace.md) company profile; **job-level override** optional |
| **Requirements** | Skills and must-haves | See below |
| **Lifecycle** | **Posting expiry** (close applications after date), owner, status (draft → active) | Expiry can auto-close or flag job on overview |
| **Hiring process** | Workflow template | [hiring-workflows.md](./hiring-workflows.md) |

Workspace-level defaults (timezone, default location mode, default comp visibility) pre-fill new jobs where configured.

### Skills & requirements (“few words” + structure)

Recruiters express needs in **natural language** as well as structured tags:

- **Quick input** — Free-text lines or chips: e.g. “5+ years Node”, “React”, “startup pace”, “must have AWS”.
- **Parse & normalize** — On paste, AI generate, or explicit “Extract from description”, orchestration proposes **structured skills** (must-have / nice-to-have), experience bands, and keywords; user edits before save.
- **Priority & contribution** — Each skill can set **priority** (e.g. must-have P0 vs nice-to-have) and **contribution** weight toward overall role fit (percentages normalized on publish). Drives [AI skill analyzers](./ai-skill-analyzers.md) assignment and interview report scoring.
- **Downstream use** — Structured requirements feed workflow **screening criteria** suggestions, candidate **AI fit analysis**, and **analyzer auto-match**.

No separate “skills-only” product screen; skills live in the JD builder and sync to the job record the server uses for screening and search.

### Workspace culture & employer context (for later AI)

**Managers** (users with workspace settings permission) maintain **company / culture context** under Settings—not on every job from scratch:

- Values, culture descriptors, ways of working, team norms, “what great looks like here”, DEI or language preferences for JD tone (optional).

Stored on the **workspace** profile ([workspace.md](./workspace.md)). Job creation **inherits** this for AI and analysis; jobs may add role-specific culture notes in an optional section.

When analysing candidates ([candidate-profile.md](./candidate-profile.md)), orchestration combines **job requirements + JD + workspace culture profile + resume/application** to produce fit summaries, gaps, and culture-alignment notes—all labeled AI-suggested, never auto-hire decisions.

### Hiring process & screening (link)

When a **workflow template** is attached, JD **skills and requirements** feed **AI-suggested screening criteria and default cutoffs** for resume/screening rounds ([hiring-workflows.md](./hiring-workflows.md)). User edits in the workflow/screening step before publish.

### Publish

**Save draft** at any step; **Publish** validates required fields, attaches workflow if selected, sets job **active** (or scheduled per product rules). Dirty-form warning on navigate away; debounced autosave when API supports.

## Plan

1. **API** — Job draft payload (all sections above), publish transition, `POST parse-jd` (paste), `POST generate-jd` (AI), attachment to workflow template ID; workspace company profile read for defaults.
2. **Phase A** — Creation entry: paste / AI / blank; basics + rich description; persist draft.
3. **Phase B** — Posting fields: location, compensation, about company (inherit workspace), posting expiry; enums synced with backend.
4. **Phase C** — Skills/requirements: free-text input + extract/normalize; skill tags; **priority + contribution** per skill.
5. **Phase D** — AI actions (improve description, inclusive language, generate from prompt); suggest screening criteria payload when workflow bound.
6. **Phase E** — Hiring process: Hyreefy default or workspace template; link to workflow editor ([hiring-workflows.md](./hiring-workflows.md)).
7. **Dependencies** — Workspace company profile (step 3); jobs domain; AI UX components; workflow list API.

## Dev

| Piece | Implementation |
|-------|----------------|
| Components | `domains/jobs/components/jd-builder/` — entry step, section components, `PasteJdStep`, `SkillsRequirementsSection` |
| Form | `useZodForm` + `jobs/schemas/jd.schema.ts`; wizard with shared schema |
| Mutations | `useSaveJobDraft`, `usePublishJob`, `useParseJd`, `useGenerateJd` |
| Workspace | Read `useWorkspace()` for about-company default and culture id/snapshot on AI calls |
| AI | `components/ai/AIAction` → `jobs/api/ai-jd-api.ts` |
| Route | `(dashboard)/jobs/new` composes wizard; `[jobId]/jd` reuses same form |

- **Permissions:** `jobs.create` / `jobs.edit`; workspace culture settings via `workspace.settings` (or catalog equivalent).
- **Tests:** Zod schema unit tests; parse/generate fixture tests; E2E paste → edit skills → publish.

## Acceptance criteria

- [ ] Create job via paste, AI generate, or blank; all converge on editable structured draft
- [ ] Paste and AI populate description and proposed skills/requirements; user must confirm before publish
- [ ] Posting fields: location, compensation (with visibility), about company (workspace default + override), posting expiry
- [ ] Skills/requirements accept free-text and structured tags; extract from description available
- [ ] Skill priority and contribution weights validated on publish; used by analyzer assignment
- [ ] Workspace culture profile editable in settings and included in server AI candidate/JD context
- [ ] Draft vs published UI states; workflow template linked on publish when selected
- [ ] JD requirements available to screening-criteria suggest when workflow includes screening round

## Non-goals

- Running assessments from this screen
- Public job board CMS (only fields needed for Hyreefy + export/integration hooks)
- Client-side-only parsing or culture scoring

## References

- [jobs.md](./jobs.md)
- [workspace.md](./workspace.md)
- [hiring-workflows.md](./hiring-workflows.md)
- [candidate-profile.md](./candidate-profile.md)
- [ai-ux.md](../architecture/ai-ux.md)
- [ai-skill-analyzers.md](./ai-skill-analyzers.md)
