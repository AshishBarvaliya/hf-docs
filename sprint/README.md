# Sprint tracking

**Active files:**

- Client: [`client/current.yaml`](./client/current.yaml)
- Server: [`server/current.yaml`](./server/current.yaml)

**Delivery model:** [`.cursor/rules/product-delivery-principles.mdc`](../.cursor/rules/product-delivery-principles.mdc) — one feature at a time, dependency order, client + server hand in hand, production-ready slices. For cross-cutting work, keep **`current_focus`** aligned across both YAML files.

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

## Agent workflow

1. **Start of work** — Read the relevant `current.yaml` (client and/or server). Confirm work matches `current_focus` or update focus when the user changes direction.
2. **During work** — Link code changes to the active task; add tasks/features/epics if scope is new.
3. **End of work** — Update task status, `current_focus`, `last_updated`, and recalculate `progress`.

## Sprint lifecycle

- **Start:** Copy `current.yaml` to `archive/<sprint-id>.yaml` under the same `client/` or `server/` folder, reset `current.yaml` for the new sprint.
- **Review:** Use `progress` and open tasks to summarize burndown in plain language.
- **Close:** Mark sprint `status: closed` in the archive copy; ensure all done/cancelled tasks are final.

## Adding work

Prefer new **tasks** under an existing feature. Add a **feature** when the slice is distinct; add an **epic** for a new initiative. Keep titles short; put detail in linked `docs/features/`, `docs/domains/client/`, or `docs/domains/server/` specs.
