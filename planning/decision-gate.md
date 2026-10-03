# Feature → sprint decision gate

Use before promoting work from planning into an **active** sprint. If any **blocker** row is unanswered, keep the work in `planning/implementation-tasks/` or resolve via ADR.

## Promotion checklist

| # | Question | Blocker if missing |
|---|----------|-------------------|
| 1 | **User outcome** — What changes for the recruiter/hiring manager? | Yes |
| 2 | **Goal link** — Which id in [goals.md](./goals.md)? | Yes |
| 3 | **Evidence** — Feature spec + mockup row or explicit design debt | Yes |
| 4 | **Dependencies** — Roadmap steps and tables/APIs listed as done | Yes |
| 5 | **Contract** — Zod/JSON request and response documented in feature **Dev** | Yes |
| 6 | **Data** — Full entity columns per [persistence.md](../architecture/persistence.md) | Yes for new tables |
| 7 | **Tenant/security** — Permissions, workspace scope, public abuse controls | Yes for auth/public routes |
| 8 | **Scale** — Pagination, indexes, async vs sync per [api-list-conventions.md](../architecture/api-list-conventions.md) | Yes for list/aggregate APIs |
| 9 | **Observability** — Logs/metrics needed for this slice | No (note in Dev) |
| 10 | **Tests** — Server unit + API; client unit (+ e2e if new route) | Yes |
| 11 | **Rollout** — Env flags, migration order, backwards compatibility | Yes if breaking API |

## Prioritization (when multiple slices qualify)

1. **Risk reduction** — security, tenancy, data-shape fixes before UI breadth  
2. **Dependency unlock** — blocks the next roadmap step  
3. **Goal impact** — moves a measurable goal signal  
4. **Cost** — prefer smaller vertical slices over parallel half-features  

## Definition of done (sprint task)

A task is `done` in YAML only when:

- Matching lines in `sprint/<id>-progress-checklist.md` are checked (including test rows)
- Linked feature **acceptance** items for this slice are `[x]` or explicitly deferred in **Non-goals**
- `npm run lint` and relevant tests pass in each touched repo
- Paired client/server tasks for the same contract are both `done` or spec defers one side

## Related

- [planning/README.md](./README.md)
- [product-delivery-principles.mdc](../.cursor/rules/product-delivery-principles.mdc)
- [delivery-tests-and-progress.mdc](../.cursor/rules/delivery-tests-and-progress.mdc)
