# Hyreefy — product & engineering docs

Workspace-level documentation for **client** (Next.js UI) and **server** (NestJS API). Specs are the source of truth for product behavior, delivery order, and architecture.

**Future:** This tree is intended to become its own git repo and absorb additional products (meeting room, AI, coding system, etc.). Shared agent rules live in [`.cursor/rules/`](./.cursor/rules/README.md); add product-specific content under a clear prefix (e.g. `products/<name>/`) when those land.

Start at [architecture/overview.md](./architecture/overview.md) (client) and [architecture/server/overview.md](./architecture/server/overview.md) (API). Production platform: [architecture/aws-platform.md](./architecture/aws-platform.md), async AI/reports: [architecture/async-jobs.md](./architecture/async-jobs.md). Feature index: [features/README.md](./features/README.md).

## Layout

| Path | Purpose |
|------|---------|
| `sprint/client/current.yaml` | Client delivery state, epics, tasks, `current_focus` |
| `sprint/server/current.yaml` | Server delivery state (keep `current_focus` aligned with client for cross-cutting work) |
| `sprint/README.md` | Sprint schema, progress %, agent workflow |
| `roadmap/frontend.md` | Delivery **order** for the full product |
| `features/` | End-to-end specs (Plan + Dev) for each product area |
| `domains/client/` | Client domain specs mirroring `client/src/domains/<name>/` |
| `domains/server/` | Server domain specs mirroring `server/src/domains/<name>/` |
| `architecture/` | Client boundaries, permissions, integrations, design system, AI UX, **multi-tenancy** |
| `architecture/server/` | API orchestration boundaries and modules |
| `adr/` | Architecture decision records (incl. [004 subdomain tenancy](./adr/004-subdomain-multi-tenancy.md), [005 AWS platform](./adr/005-aws-platform.md), [006 async AI workers](./adr/006-async-ai-workers.md)) |
| `planning/` | Brainstorming workflow and deferred implementation tasks (no app code) |

## Writing specs

Feature docs stay brief on UX prose but explicit on **Plan** (phases, APIs, dependencies) and **Dev** (routes, domains, hooks, tests). Update docs in the same change set as code.

## Cursor rules map

**Canonical (this repo):** [`.cursor/rules/README.md`](./.cursor/rules/README.md) — layout, delivery, planning, docs-driven, sprint.

| Where | Topic | Rule |
|-------|-------|------|
| **Docs repo** | Layout, delivery, planning, DDD, sprint | `.cursor/rules/*.mdc` |
| Client | Hyreefy product context | `client/.cursor/rules/hyreefy-product.mdc` (symlinks to docs rules for DDD + sprint) |
| Server | Hyreefy API context | `server/.cursor/rules/hyreefy-product.mdc` |
| `ms/` workspace | Git + client/server coordination | `ms/.cursor/rules/cross-project-coordination.mdc` (not in docs-only checkout) |

Hyreefy `ms/.cursor/rules/` **symlinks** the five shared `.mdc` files from `docs/.cursor/rules/` so Cursor loads them when the workspace root is `ms/`.
