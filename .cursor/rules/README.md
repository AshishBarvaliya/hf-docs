# Cursor rules (docs repo)

**Canonical** rules for documentation, planning, sprint tracking, and docs-driven delivery. When this tree is a **standalone repo**, open it in Cursor (or add as a workspace folder) so agents load `.cursor/rules/` from here.

## Today (Hyreefy workspace)

`ms/` symlinks these files into `ms/.cursor/rules/` and `client/` / `server/` link repo-specific entry rules. App code repos stay thin; **do not duplicate** rule bodies outside this folder.

## Rules

| File | Purpose |
|------|---------|
| `docs-layout.mdc` | Where specs, sprint YAML, ADRs, and planning artifacts live |
| `consuming-repos/docs-driven-development.mdc` | Read/update docs before and with code (**client/server only** — symlinked from app repos) |
| `consuming-repos/sprint-progress.mdc` | `sprint/*/current.yaml` workflow (**client/server only**) |
| `product-delivery-principles.mdc` | Vertical slices, deps first, no shortcuts, prod-ready |
| `brainstorming-planning-mode.mdc` | Planning-only sessions; implementation task queue |

## Multi-product (future)

This repo will grow beyond Hyreefy (e.g. meeting room, AI, coding system). Add product-specific specs under a clear prefix (e.g. `products/<name>/`) and optional `products/<name>/.cursor/rules/` for stack-specific hints. **Shared** rules above stay at this level unless a product needs an override.

## Hyreefy workspace-only

Cross-repo git and `client/` + `server/` coordination: `ms/.cursor/rules/cross-project-coordination.mdc` (not shipped in docs-only checkouts unless you copy workspace meta rules).
