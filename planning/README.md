# Planning & brainstorming (no code)

Use this workflow when you want to **think through features with the agent** and later **sync documentation**—without touching application code in the same pass.

**Cursor rule:** [`.cursor/rules/brainstorming-planning-mode.mdc`](../.cursor/rules/brainstorming-planning-mode.mdc) (canonical in this docs repo)

## Why separate planning from implementation

- Keeps chat focused on product intent, dependencies, and acceptance criteria.
- Specs and sprint YAML stay the source of truth before any PR.
- Deferred engineering work lives in **implementation task files**, not half-done code on a branch.

## How to run a session

### 1. Enter planning mode

Say any of:

- `planning mode` / `brainstorming mode` / `docs only`
- Or jump straight to: `let's discuss <feature>` (then ask to sync docs when ready)

The agent should **not** edit `client/src` or `server/src` until you exit planning.

### 2. Discuss freely

Topics that belong here:

- User journeys, permissions, API contracts, edge cases
- **Entity shape for later features** — columns this slice will not display but the table must store ([persistence.md](../architecture/persistence.md))
- Roadmap order and dependencies
- What is in scope vs **non-goals**
- Cross-cutting client + server alignment

The agent may read code and existing docs for context.

### 3. Sync documentation

When you are ready, ask explicitly:

- `update docs from our discussion`
- `sync plan, goals, and feature specs`

The agent updates the relevant **planning artifacts** (see below) and files **implementation tasks** for anything that still requires code.

### 4. Exit planning

- `exit planning` — done for now; no implementation yet
- `start implementing` / `build it` — normal delivery rules apply; use sprint tasks + feature spec as usual

## What gets updated (planning artifacts)

| Artifact | Path | When to touch |
|----------|------|----------------|
| Feature spec | `docs/features/<area>.md` | Behavior, Plan phases, Dev map, acceptance criteria |
| Roadmap | `docs/roadmap/frontend.md` | Delivery order or dependencies change |
| Client sprint | `docs/sprint/client/current.yaml` | Goals, focus, epics/features (not necessarily every deferred task) |
| Server sprint | `docs/sprint/server/current.yaml` | Same for API-side focus and goals |
| Client domain doc | `docs/domains/client/<name>/README.md` | Client domain boundaries |
| Server domain doc | `docs/domains/server/<name>/README.md` | Server contract or domain boundaries |
| Tenancy architecture | `docs/architecture/multi-tenancy.md` | Subdomain flow, tenant vs workspace |
| ADR | `docs/adr/*.md` | Durable architectural decisions (e.g. 004 subdomain tenancy) |
| Session notes (optional) | `docs/planning/sessions/` | Long discussions before spec edits |
| Sprint checklist | `docs/sprint/<sprint-id>-progress-checklist.md` | Checkbox burndown + % (tests as separate lines) |

Sprint editing rules: [`docs/sprint/README.md`](../sprint/README.md). Tests and progress: [`delivery-tests-and-progress.mdc`](../.cursor/rules/delivery-tests-and-progress.mdc).

## Implementation task queue (code later)

Anything that requires code—but is **not** being built in the current chat—goes here:

**Folder:** [`implementation-tasks/`](./implementation-tasks/)

- One file per slice (e.g. `2026-09-29-rbac-can-component.md`)
- Copy from [`_template.md`](./implementation-tasks/_template.md)
- Link from the updated feature spec or sprint feature `doc:` / notes when you promote to `todo` tasks

Do **not** use this folder for product prose that belongs in `docs/features/`. Use it for **actionable engineering checklists** (paths, APIs, tests).

## Quick reference

```text
Discuss → "update docs" → specs + sprint goals + implementation-tasks/*.md
Later  → "build it"     → product-delivery-principles + sprint tasks + code
```

## Related

- Delivery model: [`.cursor/rules/product-delivery-principles.mdc`](../.cursor/rules/product-delivery-principles.mdc)
- Cross-project (Hyreefy `ms/` workspace): [`../../.cursor/rules/cross-project-coordination.mdc`](../../.cursor/rules/cross-project-coordination.mdc)
- Shared doc paths: [`.cursor/rules/docs-layout.mdc`](../.cursor/rules/docs-layout.mdc)
- Docs index: [`README.md`](../README.md)
