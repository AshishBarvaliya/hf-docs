# Sprint definition template

Copy this when opening a **new** sprint. Do **not** start `client/` or `server/` implementation until every **Required sections** block below is filled and paired YAML + checklist exist.

**Lifecycle:** [`README.md`](./README.md) · **Gate rule:** [`../.cursor/rules/sprint-before-code.mdc`](../.cursor/rules/sprint-before-code.mdc)

---

## 1. Header

| Field | Value |
|-------|--------|
| **Sprint id** | `sprint-<short-name>` (kebab-case, unique) |
| **Name** | Human title |
| **Goal** | One sentence — shippable outcome |
| **Dates** | `start` / `end` (ISO) |
| **Roadmap / feature links** | e.g. STEP 6 · `features/candidate-profile.md` (Phase C only) |
| **Status** | `active` |

---

## 2. Scope (required in checklist intro)

### In scope

- Bullet list of **this sprint only** (routes, APIs, screens, behaviors).

### Non-goals (deferred)

- Everything from the parent feature/roadmap **not** in this sprint (integrations, later phases, polish).

---

## 3. Data / DB (required)

Document in feature spec **Dev → Data** and in sprint tasks.

| Item | This sprint |
|------|-------------|
| New/changed tables or columns | List migration id(s) or **“None”** with justification |
| `stored now, API later` columns | Listed if new entity |
| Seed / fixtures | Yes/no — what dev data |

**YAML:** At least one server task, e.g. `task-<entity>-migration` **or** `task-<sprint>-no-db-changes`.

---

## 4. Contract (required if HTTP changes)

| Method | Path | Permission | Notes |
|--------|------|------------|--------|
| | | | |

Update Zod on server; client fetchers/types in the same sprint.

---

## 5. Task breakdown (paired client + server YAML)

Weights are optional (default 1). Every sprint **must** include explicit test tasks.

### Server (`sprint/server/current.yaml`)

| Task id | Title | Status |
|---------|--------|--------|
| `task-*-migration` or `task-*-no-db` | DB slice or documented none | `todo` |
| `task-*-api` | Routes + service + guards | `todo` |
| `task-*-server-unit` | Unit: schemas, service, guards | `todo` |
| `task-*-server-e2e` | API e2e per changed route | `todo` |

### Client (`sprint/client/current.yaml`)

| Task id | Title | Status |
|---------|--------|--------|
| `task-*-ui` | Pages/components/hooks | `todo` |
| `task-*-client-unit` | Unit: hooks, helpers, `<Can>` | `todo` |
| `task-*-playwright` | Playwright for new/changed flows | `todo` |

`current_focus` must match **one** `in_progress` task across repos when cross-cutting.

---

## 6. Checklist file (`sprint/<sprint-id>-progress-checklist.md`)

Minimum sections — **one checkbox per line**:

```markdown
# Sprint <id> — progress checklist

**Sprint id:** `<id>` (**active**)
**Scope:** …
**Non-goals:** …

| Area | Done | Total | % |
|------|------|-------|---|

## Data / DB
- [ ] …

## Server
- [ ] …
- [ ] Server unit tests …
- [ ] Server API e2e …

## Client
- [ ] …
- [ ] Client unit tests …
- [ ] Playwright …

## Docs
- [ ] Feature spec Dev (Data, Tests) + domain READMEs if boundaries changed
```

---

## 7. Definition of done (sprint)

- [ ] All in-scope checklist lines checked (including **all** test lines).
- [ ] `npm run lint` + relevant `npm run test` / `test:e2e` / `test:unit` pass in touched repos.
- [ ] `progress.sprint_percent` = 100 in both YAML files; sprint `status: closed`; archive copies written.

---

## 8. YAML skeleton (paste into `current.yaml`)

```yaml
sprint:
  id: sprint-example
  name: Example sprint
  goal: One sentence.
  dates:
    start: "YYYY-MM-DD"
    end: "YYYY-MM-DD"
  status: active

current_focus:
  epic_id: epic-example
  feature_id: feat-example
  task_id: task-example-first
  goal: Same as sprint goal or current slice.

progress:
  sprint_percent: 0
  epics:
    epic-example: 0
```

Add `scope` and `non_goals` as comments at the top of the checklist (or in the feature spec **Plan** subsection for the active slice).
