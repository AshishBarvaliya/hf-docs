# Hiring workflow builder

**Routes:** `(dashboard)/automations` (templates), `(dashboard)/jobs/[jobId]/settings` (job workflow), job **Pipeline** tab  
**Domains:** `pipeline`, `automations`, `jobs`  
**Roadmap step:** 7

## Product behavior

A **hiring workflow** is the ordered set of **rounds** (stages) a candidate moves through for a job—from application through screening, assessments, interviews, and offer. Workflows drive kanban columns, stage permissions, integration handoffs, and automation triggers.

### Templates: Hyreefy defaults and workspace custom

Workspace **admins** (permission-gated, e.g. `workflows.manage`) configure hiring in the **Automations / workflows** area:

| Source | Behavior |
|--------|----------|
| **Hyreefy default templates** | Curated starting workflows (e.g. “Standard tech hire”, “High-volume screening”) shipped by the platform. Read-only originals; **clone** into the workspace to customize. |
| **Workspace templates** | Created from scratch or cloned from a default. Reusable across jobs; versioned draft → publish. |
| **Job workflow** | On publish or job settings, attach a **published workspace template** or customize a **job-specific** copy. Published job workflow drives that job’s pipeline. |

Managers and recruiters use the workflow on the job; only users with workflow edit permissions change rounds and automation settings.

### Round types (stage catalog)

Each round has a **type** from a backend **stage type registry** (extensible as integrations land). Examples:

| Round type | Purpose | Typical integrations / config |
|------------|---------|--------------------------------|
| **Application** | Intake; resume/CV on file | Required fields, source tracking |
| **Resume / profile screening** | Human or rules-based gate on CV vs job | **Screening criteria** (see below), auto-filter / auto-reject |
| **Assessment** | Skills or aptitude test | External assessment provider, pass score, time limit |
| **Coding** | Live or async coding exercise | Coding platform route, rubric link |
| **Interview (F2F / video)** | Structured conversation rounds | Meeting platform, panel, duration, **auto-schedule** options, **AI skill analyzer** for recording reports ([ai-skill-analyzers.md](./ai-skill-analyzers.md)) |
| **Offer** | Offer stage | Notifications; optional approval step later |

Round order is **linear by default**; conditional branches (e.g. fail assessment → reject) follow backend capability ([Phase D](#plan)).

### Per-round automation (configure in workflow editor)

Each round can declare **what happens automatically** when a candidate **enters** or **completes** the round (execution on **server**; client configures):

- **Email** — Template + merge fields (e.g. “assessment invite”, “interview confirmed”); see [emails.md](./emails.md).
- **Schedule meeting** — For interview rounds: trigger scheduling flow (send booking link, or auto-propose slots per policy); see [interviews.md](./interviews.md).
- **Assign assessment / coding** — Deep link or provision attempt on external product; see [external-integrations.md](./external-integrations.md).
- **Move stage** — On pass/fail thresholds (assessment score, screening outcome).
- **Reject** — Terminal disposition with reason code (including **auto-reject** from screening—below).

Flags are stored on the published workflow; [automations.md](./automations.md) covers workspace-level rules that reference the same triggers.

### Screening criteria, cutoffs, and auto-filter / auto-reject

**Resume / profile screening** rounds (and optionally early application gates) support **structured criteria** aligned to the job:

1. **AI-suggested defaults** — When a job has JD **requirements and skills** ([jd-builder.md](./jd-builder.md)), orchestration can propose criteria (must-have skills, years of experience, education, keywords) and **default cutoffs** (minimum match score, knockout rules). Output is **suggested**, visually distinct per [ai-ux.md](../architecture/ai-ux.md).
2. **Admin / hiring manager edit** — Users with workflow edit permission can add, remove, or change criteria and cutoffs before publish; changes apply to the job’s published workflow version.
3. **Server enforcement** — On apply or stage entry, the **server** evaluates the candidate against published criteria (using scored signals from resume parsing / application data—not client-only logic).
4. **Outcomes** (configurable per screening round):
   - **Auto-reject** — Fail knockout or below cutoff → disposition `rejected` with reason (e.g. `screening.criteria_not_met`); optional notification email.
   - **Auto-filter** — Hide or deprioritize in recruiter views without terminal reject (e.g. “below cutoff” bucket) if the workspace policy allows soft filtering.
   - **Manual review** — Flag only; recruiter decides.

Recruiters see **why** a candidate was auto-rejected or filtered (criteria snapshot + score); overrides follow permissions and are audited where [permissions-audit.md](./permissions-audit.md) applies.

### User journeys (summary)

```text
Admin → Automations → pick Hyreefy default OR create template → set rounds
     → per round: type, emails, meetings, assessment/coding routes
     → screening round: review AI-suggested criteria/cutoff → edit → publish template

Hiring manager → Job settings / JD → attach template (or customize job copy)
              → publish job workflow → kanban + automations live

Candidate → apply → server screening → auto-reject | auto-filter | advance to next round
Recruiter  → ATS sees outcome + can override if permitted
```

## Plan

1. **API** — Workflow CRUD (workspace template + job instance), version/draft/publish, stage type registry, validate-on-publish, screening criteria schema, **evaluate screening** on apply/stage entry, disposition + audit events.
2. **Phase A** — Linear round list editor; save draft workflow on job; publish ties to pipeline columns.
3. **Phase B** — Round detail drawer: assessment, coding, interview, email types; notification and auto-schedule toggles.
4. **Phase C** — Template gallery (Hyreefy defaults + workspace list); clone to job or workspace; link from JD builder.
5. **Phase D** — Screening round UI: criteria list, cutoffs, AI “Suggest from JD” action; wire auto-reject / auto-filter policies.
6. **Phase E** — Branching/conditional paths if backend supports (UI follows API capability).
7. **Dependencies** — JD requirements for AI suggest (step 4); assessment/meeting/email integrations (steps 8–10); RBAC keys for workflow edit vs read.

## Dev

| Piece | Implementation |
|-------|----------------|
| Domain | `src/domains/pipeline/` — workflow types, `useWorkflow`, `useWorkflowMutations`, screening criteria types |
| Automations | `src/domains/automations/` — template library UI (defaults + workspace) |
| Editor | Sortable round list, `StageConfigDrawer`, provider pickers, `ScreeningCriteriaEditor` |
| AI | `AIAction` “Suggest criteria from JD” → orchestration API; merge into draft only after user confirms |
| Validation | Zod schemas mirroring backend publish rules (including criteria + cutoff bounds) |

- **No embedded assessment or meeting UI** — configuration, deep links, and scheduling triggers only.
- **Sync:** Publishing workflow invalidates pipeline kanban and candidate list queries for affected jobs.
- **Tests:** Schema tests for stage config and screening criteria; API contract tests for evaluate-screening; E2E publish workflow and verify auto-reject path on fixture candidate.

## Acceptance criteria

- [ ] Hyreefy default templates visible; clone to workspace; create/edit/publish workspace templates
- [ ] Job can attach published template or job-specific published workflow
- [ ] Round types configurable per registry (resume screening, assessment, coding, interview, offer, …)
- [ ] Per-round email and auto-meeting (or booking) settings persisted on publish
- [ ] AI suggests screening criteria/cutoffs from job JD; user can edit before publish
- [ ] Server auto-reject and/or auto-filter per screening config; ATS shows reason; no client-only enforcement
- [ ] Kanban reflects published rounds; automation triggers fire from server on stage transitions

## Non-goals

- Building assessment, coding, or meeting products inside Hyreefy (orchestration only)
- Fully autonomous hiring with no human override on reject (override remains permission-gated)
- Client-side execution of automations or screening decisions

## References

- [integrations.md](../architecture/integrations.md)
- [automations.md](./automations.md)
- [jd-builder.md](./jd-builder.md)
- [candidates-ats.md](./candidates-ats.md)
- [ai-ux.md](../architecture/ai-ux.md)
- [../domains/server/pipeline/README.md](../domains/server/pipeline/README.md)
