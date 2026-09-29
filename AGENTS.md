# Docs repository

Product specs, sprint state, ADRs, architecture, and planning workflow for Hiring OS and (later) sibling products (meeting room, AI, coding system, etc.).

## Start here

- [README.md](./README.md) — layout index
- [planning/README.md](./planning/README.md) — brainstorming, no code
- [.cursor/rules/README.md](./.cursor/rules/README.md) — agent rules that ship **with** this repo

## Consuming repos

App and service repos should:

1. Mount this tree as `../docs/` (submodule, sibling folder, or workspace multi-root).
2. Symlink `consuming-repos/*.mdc` from this repo into the app’s `.cursor/rules/` (see `.cursor/rules/README.md`).
3. Never fork feature specs into `client/docs/` or `server/docs/` for product content.

## Hiring OS workspace

When `docs/` sits next to `client/` and `server/` under `ms/`:

- Workspace meta rules stay in `ms/.cursor/rules/` (`workspace-layout`, `git-project-scope`, `cross-project-coordination`).
- Canonical doc rules are **here**; `ms/.cursor/rules/` symlinks the shared `.mdc` files for Cursor to load them.
