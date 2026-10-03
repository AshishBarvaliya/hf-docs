# Sprint tracking

**Active files:**

- Client: [`client/current.yaml`](./client/current.yaml) — `sprint-candidate-profile-visual-fidelity` (**active**)
- Server: [`server/current.yaml`](./server/current.yaml) — paired `sprint-candidate-profile-visual-fidelity` (**active**)
- **Candidate profile visual fidelity checklist:** [`sprint-candidate-profile-visual-fidelity-progress-checklist.md`](./sprint-candidate-profile-visual-fidelity-progress-checklist.md)
- **Candidate profile visual fidelity dev guide:** [`sprint-candidate-profile-visual-fidelity-development-guide.md`](./sprint-candidate-profile-visual-fidelity-development-guide.md)
- Previous: `sprint-overview-visual-fidelity` (**closed**; archives + [`sprint-overview-visual-fidelity-progress-checklist.md`](./sprint-overview-visual-fidelity-progress-checklist.md))
- **Overview visual fidelity dev guide:** [`sprint-overview-visual-fidelity-development-guide.md`](./sprint-overview-visual-fidelity-development-guide.md)
- **Shell visual fidelity checklist (closed):** [`sprint-shell-visual-fidelity-progress-checklist.md`](./sprint-shell-visual-fidelity-progress-checklist.md)
- Previous: `sprint-auth-visual-fidelity` (**closed**; archives + [`sprint-auth-visual-fidelity-progress-checklist.md`](./sprint-auth-visual-fidelity-progress-checklist.md))
- Previous sprint: `sprint-candidate-profile-polish` (**closed**; archives: [`client`](./client/archive/sprint-candidate-profile-polish.yaml), [`server`](./server/archive/sprint-candidate-profile-polish.yaml))
- **Sprint candidate profile polish checklist** (closed): [`sprint-candidate-profile-polish-progress-checklist.md`](./sprint-candidate-profile-polish-progress-checklist.md)
- **Sprint candidate profile Phase B checklist** (closed): [`sprint-candidate-profile-phase-b-progress-checklist.md`](./sprint-candidate-profile-phase-b-progress-checklist.md)
- **Sprint candidate profile Phase A checklist** (closed): [`sprint-candidate-profile-progress-checklist.md`](./sprint-candidate-profile-progress-checklist.md)
- Archives: `client/archive/sprint-candidate-profile-polish.yaml`, `server/archive/sprint-candidate-profile-polish.yaml`, `client/archive/sprint-candidate-profile-phase-b.yaml`, `server/archive/sprint-candidate-profile-phase-b.yaml`, `client/archive/sprint-candidate-profile.yaml`, `server/archive/sprint-candidate-profile.yaml`
- **Sprint ATS UI checklist** (closed): [`sprint-ats-ui-progress-checklist.md`](./sprint-ats-ui-progress-checklist.md)
- **Sprint ATS foundation checklist** (closed): [`sprint-ats-foundation-progress-checklist.md`](./sprint-ats-foundation-progress-checklist.md)
- Archives: `client/archive/sprint-ats-ui.yaml`, `server/archive/sprint-ats-ui.yaml`, `client/archive/sprint-ats-foundation.yaml`, `server/archive/sprint-ats-foundation.yaml`
- **Sprint 1 implementation guide** (routes, API, DB): [`sprint-1-development-guide.md`](./sprint-1-development-guide.md)
- **Sprint 1 checkbox tracker** (closed): [`sprint-1-progress-checklist.md`](./sprint-1-progress-checklist.md)
- **Sprint 2 development guide** (routes, API, prod/perf): [`sprint-2-development-guide.md`](./sprint-2-development-guide.md)
- **Sprint 2 checkbox tracker** (closed): [`sprint-2-progress-checklist.md`](./sprint-2-progress-checklist.md)
- **Sprint integrity** (closed remediation): [`sprint-integrity-progress-checklist.md`](./sprint-integrity-progress-checklist.md)

**Delivery model:** [`.cursor/rules/product-delivery-principles.mdc`](../.cursor/rules/product-delivery-principles.mdc) — one feature at a time, dependency order, **end-to-end slices** (contract + DB + server + client + tests), production-ready.

**Sprint before code (mandatory):** Do not implement from [roadmap](../roadmap/frontend.md) or full [feature specs](../features/README.md) without an **active** sprint. Every new sprint must define **scope**, **goal**, **DB/migration tasks** (or explicit none), **server unit + API e2e**, **client unit + Playwright**, and a checklist. Template: [`sprint-definition-template.md`](./sprint-definition-template.md). Rule: [`.cursor/rules/sprint-before-code.mdc`](../.cursor/rules/sprint-before-code.mdc).

**Data model:** [architecture/persistence.md](../architecture/persistence.md). A database task is done only when the table matches the **whole entity** (later features’ columns included, marked `stored now, API later`), not when the current test has a column to assert. For cross-cutting work, keep **`current_focus`** aligned across both YAML files (same `feature_id` / goal when the slice spans repos).

## Hierarchy

| Level | Meaning |
|-------|---------|
| **Sprint** | Time-boxed iteration with a single sprint-level goal |
| **Epic** | Large outcome (often a product area or initiative) |
| **Feature** | Shippable slice under an epic; optional `doc:` link to specs under `docs/` |
| **Task** | Concrete work item with `status` and optional `weight` (default 1) |

## Task status

`todo` · `in_progress` · `done` · `blocked` · `cancelled`

Only one task should be `in_progress` at a time unless the user explicitly parallelizes work. That task should match `current_focus.task_id` in the tracker you are updating (and the sibling tracker when the slice is cross-cutting).

## Progress % (recalculate when tasks change)

1. **Feature** = `sum(weight of done tasks) / sum(all task weights) × 100` (round to integer). Ignore `cancelled` tasks in the denominator.
2. **Epic** = weighted average of its features (weight = each feature’s total task weight).
3. **Sprint** = weighted average of all non-cancelled tasks in the sprint.

Write results under `progress.sprint_percent` and `progress.epics.<epic_id>`.

`in_progress` counts as **0%** toward done unless the user asks to count partial credit.

### Checkbox checklist (human burndown)

For the active sprint, maintain **`sprint/<sprint-id>-progress-checklist.md`**:

- One `- [ ]` line per deliverable (implementation **and** tests — separate lines for **API unit/e2e** and **UI unit**).
- Update the summary table (done / total / %) when checking items.
- Report **YAML %** and **checklist %** together in status lines (see `delivery-tests-and-progress.mdc`).

A sprint task is **not done** in YAML until its matching checklist lines (including tests) are checked and test commands pass in the touched repo(s).

**Design / route DoD:** Canonical mockup rows in [design-implementation-fidelity.md](../architecture/design-implementation-fidelity.md) must be checked in feature acceptance or deferred in **Non-goals**; nav must not link to unshipped routes.

## Tests (required every slice)

| Change | Server | Client |
|--------|--------|--------|
| New/changed HTTP route | Unit + e2e/supertest for that route | Fetchers/hooks unit tests if logic added |
| New/changed UI route or form | — | Unit for helpers/hooks; Playwright when flow is critical |
| Guards / permissions | Unit matrix + API 401/403 | Unit for `<Can>` / `usePermissions` |

Details: [`../.cursor/rules/delivery-tests-and-progress.mdc`](../.cursor/rules/delivery-tests-and-progress.mdc).

## Agent workflow

1. **Before any code** — If there is no **active** sprint for the request, complete [`sprint-definition-template.md`](./sprint-definition-template.md) (paired YAML + checklist + scope/non-goals + DB + test tasks). Roadmap/feature docs alone are insufficient.
2. **Start of work** — Read the relevant `current.yaml` (client and/or server). Confirm work matches `current_focus` or update focus when the user changes direction.
3. **During work** — Link code changes to the active task; **do not** expand scope without new sprint tasks and checklist lines (prefer a follow-up sprint for new scope).
4. **End of work** — Update task status, `current_focus`, `last_updated`, recalculate `progress`, and check off **`sprint/<id>-progress-checklist.md`** (including test rows).

## Sprint lifecycle

- **Define:** Fill template sections (scope, DB, tests) → write checklist → set `status: active` on both YAML files.
- **Start:** Copy `current.yaml` to `archive/<sprint-id>.yaml` under the same `client/` or `server/` folder, reset `current.yaml` for the new sprint.
- **Review:** Use `progress` and open tasks to summarize burndown in plain language.
- **Close:** Mark sprint `status: closed` in the archive copy; ensure all done/cancelled tasks are final.

## Adding work

Prefer new **tasks** under an existing feature. Add a **feature** when the slice is distinct; add an **epic** for a new initiative. Keep titles short; put detail in linked `docs/features/`, `docs/domains/client/`, or `docs/domains/server/` specs.

## End-to-end tasks (client + server YAML)

When a roadmap feature needs new APIs or tables, split work into paired tasks (same feature, both YAML files):

| Server task (examples) | Client task (examples) |
|------------------------|-------------------------|
| Schema + migration + seed | Types + fetchers aligned to contract |
| Domain module + routes + guards | Hooks, pages, `<Can>` |
| API unit + e2e for routes | UI unit + e2e smoke |

Do not mark the **client** feature task `done` if the server task for the same contract is still `todo` (unless the spec explicitly defers server—rare). Server-only schema work still belongs in `sprint/server/current.yaml` under the matching feature id.
