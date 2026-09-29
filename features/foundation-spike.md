# Foundation spike (not the product)

Early code on `/` and `/candidates` (sample table, pipeline chart, mock API) was a **technical spike** to prove domains, TanStack Query, and E2E. It is **not** the shipped product experience.

Full product replaces this with:

- `(dashboard)/overview` as recruiter home ([overview-dashboard.md](./overview-dashboard.md))
- Real APIs and permissions throughout
- Complete feature set in [README.md](./README.md)

When refactoring, **migrate** patterns from the spike into dashboard routes; do not treat the spike layout as the final UX.
