# <Title>

**Status:** proposed | ready | in_sprint | done  
**Created:** YYYY-MM-DD  
**Feature spec:** [docs/features/<area>.md](../../features/<area>.md)  
**Session notes (optional):** [../sessions/YYYY-MM-DD-<topic>.md](../sessions/YYYY-MM-DD-<topic>.md)

## Summary

One paragraph: what we are building and why, after the planning discussion.

## Out of scope (this task)

Bullets — tie to feature **Non-goals** where possible.

## Client

- [ ] Implementation: …
- [ ] **UI unit tests** (hooks, helpers, components with logic)
- [ ] **E2E** (if new route or critical flow)
- Paths: `client/src/...`

## Server

- [ ] Implementation: …
- [ ] **API unit tests** (services, guards, schemas)
- [ ] **API e2e** (supertest for each new/changed route)
- Paths: `server/src/...`

## Data (server)

- [ ] Drizzle schema changes, migration in `server/drizzle/`, seed/fixtures if needed
- Document tables in feature spec **Dev** and `domains/server/<name>/README.md`

## Contract / API

- Endpoints, payloads, permissions (server authoritative).

## Acceptance criteria

- [ ] …

## Open questions

- …

## Promotion to sprint

When starting implementation, add matching tasks to `docs/sprint/client/current.yaml` and `docs/sprint/server/current.yaml` and set this file’s status to `in_sprint`.
