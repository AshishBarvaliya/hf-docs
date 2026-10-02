# Persistence — design the table for the product, not the sprint

Hyreefy is one hiring system. Roadmap steps decide **when UI and APIs ship**. They do not decide **how small a table may be**.

When a table is created or altered, design it for **every feature that will read or write that entity**, including features later on the roadmap. A column that this slice’s API does not return is still part of the table if a later feature needs it. Adding it now is the plan. Adding it in a “we’ll widen the table later” migration is a shortcut.

Canonical for agents: [`.cursor/rules/data-model-at-scale.mdc`](../.cursor/rules/data-model-at-scale.mdc).

## What “one feature at a time” means for data

| Ship now | Design now, even if unused by this slice’s API |
|----------|------------------------------------------------|
| The route, guard, and UI for the active feature | Columns, FKs, and indexes that **later features on this entity** need |
| Seed rows so the slice can be tested | Nullability and defaults so those columns can sit empty until that feature ships |
| Queries that filter to the current use | Soft delete and actor columns so history is possible without a second migration |

Do not create a second, “real” table later to replace a spike shape on `main`. The first migration of an entity is the production shape.

## Before writing a Drizzle table

1. Read the feature spec for **this** entity and every feature spec that names the same entity (jobs, candidates, workspace, interviews, analyzers, audit).
2. List columns those specs need, not only columns this endpoint returns.
3. Apply the **row standard** below.
4. Write the full column list in the feature **Dev → Data** section **before** the migration.
5. If a column is for a later feature, mark it in the spec as `stored now, API later` — do not omit it.

## Row standard (tenant-owned business data)

Every business table (`tenants`, `workspaces`, `users`, `workspace_members`, `roles`, `candidates`, `jobs`, and later domain tables) includes:

| Column | Why it exists before the feature that displays it |
|--------|---------------------------------------------------|
| `id` | uuid primary key |
| `created_at`, `updated_at` | Ordering, sync, support |
| `created_by`, `updated_by` | Nullable uuid → `users.id` for system seed; set when a person acts. Audit UI is step 13; the ids must already be on the row |
| `deleted_at` | Soft delete. Product lists filter `deleted_at IS NULL`. Hard delete is an exception written in the spec |

Join tables that are pure links (`role_permissions`) still get `created_at` and `created_by`. They do not need a surrogate story, but they are not a pair of foreign keys with no history.

**Catalog exceptions** (document in the spec): global permission keys may be hard-deleted only if no tenant data points at them. Do not use “it’s a small lookup” as a reason to skip timestamps.

## Look-ahead examples (not a full schema)

These are why the sprint 1 tables are too thin. Later slices must not copy that shape.

| Entity | Sprint 1 stored | Later features already specified that need more on the same row |
|--------|-----------------|------------------------------------------------------------------|
| `users` | email, name, password | Invites, session policy, disable account, audit actor ([workspace.md](../features/workspace.md), [permissions-audit.md](../features/permissions-audit.md)) |
| `workspaces` | name | Company profile, culture, timezone, posting defaults ([workspace.md](../features/workspace.md)) — stored before the settings UI, not invented when the form is built |
| `workspace_members` | user, workspace, role | Invite, who added the member, removed-but-retained membership |
| `roles` | name | Description, system vs custom, who changed the role |
| `candidates` | name, role, stage | Profile, applications, source, disposition, screening snapshot ([candidate-profile.md](../features/candidate-profile.md), [candidates-ats.md](../features/candidates-ats.md)). **`candidate_applications`** holds job, stage, score, applied ([design-implementation-fidelity.md](./design-implementation-fidelity.md)). `name` / `role` / `stage` on `candidates` is a **spike list**, not the ATS model |

New product tables (jobs, applications, interviews, analyzers) follow this doc on first create. Do not add them as id + two text columns because the first screen is a list.

## API and UI

- The active slice may **omit** future columns from JSON responses.
- The database may **not** omit them.
- Zod on the HTTP boundary validates what the route accepts today. Drizzle schema validates what the product will store.

## Sprint 1 gap

`server/drizzle/0000_bumpy_klaw.sql` and `0001_rbac.sql` were generated to satisfy auth and RBAC tests only. They are **not** the target model. `0002_foundation_schema_at_scale.sql` widens `tenants`, `workspaces`, `users`, `workspace_members`, `roles`, `permissions`, `role_permissions`, and `candidates` to this standard. Do not add the next product entity on the thin shape. Server task `task-foundation-schema-at-scale`.

## Related

- [multi-tenancy.md](./multi-tenancy.md) — workspace scope on every tenant-owned row
- [server/overview.md](./server/overview.md) — Drizzle, migrations
- [features/README.md](../features/README.md) — Dev → Data must list the full column set
