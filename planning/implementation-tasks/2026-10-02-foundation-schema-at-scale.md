# Widen foundation tables to the product data model

**Status:** done  
**Created:** 2026-10-02  
**Feature spec:** [architecture/persistence.md](../../architecture/persistence.md)

## Summary

Sprint 1 auth and RBAC tables were created with only the columns the login and `candidates.read` tests needed. The product standard is to store the full entity shape up front, including columns later features will use and the audit / soft-delete columns.

## Out of scope (this task)

- Building jobs, profile, or audit UI
- Returning every new column from existing HTTP responses

## Server

- [x] Alter `tenants`, `workspaces`, `users`, `workspace_members`, `roles`, `permissions`, `role_permissions`, `candidates` to match [persistence.md](../../architecture/persistence.md)
- [x] Include columns later features already specify (workspace profile/culture/timezone, user status, member invited_by, candidate identity beyond the spike list) as nullable or defaulted, `stored now, API later`
- [x] **API unit tests** still pass for login and candidates list; soft-delete filter covered by a unit test
- [x] Paths: `server/src/database/schema/`, `server/drizzle/`

## Client

- [x] No UI required for unused columns
- [x] Update types only if a response shape changes

## Data (server)

- [x] New migration after `0001_rbac.sql`. Do not edit applied migrations in place
- [x] Reseed acme/beta

## Contract / API

- Existing login and candidate list payloads stay stable unless a column must be exposed for the shell. New columns may be absent from JSON.

## Acceptance criteria

- [x] No business table is missing `created_by`, `updated_by`, or `deleted_at` without an exception written in the persistence doc
- [x] `candidates` is no longer treated as the long-term ATS shape in the spec (either widened toward the profile spec or explicitly marked spike with a replacement plan dated before ATS work)

## Promotion to sprint

Server task `task-foundation-schema-at-scale` in `sprint/server/current.yaml`. Do this before the next new product table.
