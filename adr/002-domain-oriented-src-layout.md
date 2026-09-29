# ADR 002: Domain-oriented `src/domains` layout

**Status:** Accepted  
**Date:** 2026-09-28

## Context

Common scaffolding uses `src/features/` for business logic and `src/app/` for routes. This repo already uses `src/domains/` with barrels, TanStack Query in `api/`, and thin `app/` routes.

## Decision

Keep **`src/domains/<name>/`** as the sole business-logic root. Do not add a parallel `src/features/` tree. Product “features” are documented in `docs/features/*.md` and implemented in one or more domains.

## Consequences

- New areas scaffold under `domains/` with `index.ts` public API.
- Cross-domain orchestration in route handlers or explicit application services, not in leaf components.
- Cursor rules and docs refer to domains, not features folder.

## References

- [architecture/folder-structure.md](../architecture/folder-structure.md)
- `.cursor/rules/domain-oriented-frontend.mdc`
