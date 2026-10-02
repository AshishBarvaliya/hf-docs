# Sprint 1 remediation — auth / RBAC proof

**Status:** done  
**Created:** 2026-10-03  
**Feature specs:** [auth.md](../../features/auth.md), [rbac.md](../../features/rbac.md)

## Summary

Harden tenant session binding on tenant hosts, enforce role/workspace join at login, route-level candidates permission UX, and live Playwright coverage without mock candidates API.

## Out of scope

- JWT revocation / session TTL changes
- `role` claim on JWT (permission list remains authoritative)

## Server

- [x] `auth.service.ts` — join `roles.workspace_id` to membership workspace
- [x] **Unit test** regression for role/workspace mismatch (existing login suite + join in service)

## Client

- [x] `gate.ts` — reject session tenant slug mismatch on tenant host
- [x] **Unit test** for host/session mismatch
- [x] Candidates route — `NoAccess` when missing `candidates.read`; handle API 403
- [x] Remove `NEXT_PUBLIC_CANDIDATES_API=mock` production path
- [x] **E2E** — login + live candidates list; interviewer no-access on `/candidates`

## Acceptance criteria

- [x] Acme session cannot browse `beta.localhost` app routes without re-login
- [x] Interviewer sees NoAccess (or equivalent) on candidates, not a broken table
