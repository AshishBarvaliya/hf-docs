# ADR 003 — Auth.js session + Nest API authorization

## Status

Accepted — 2026-09-28

## Context

Recruiter UI and orchestration API must share identity before we build dashboard shell, workspace settings, or production ATS flows. Public candidate list without auth was acceptable for the spike only.

## Decision

- **Client:** [Auth.js](https://authjs.dev/) (Next.js App Router integration) owns sign-in, session cookies, and session callbacks.
- **API:** NestJS validates the **same JWT** (shared `AUTH_SECRET`) on protected routes via a global or per-route guard.
- **Browser → API:** Client domain fetchers send `Authorization: Bearer <accessToken>` where `accessToken` is issued in Auth.js JWT/session callbacks (not an third-party API key in the client bundle).

Auth slice delivers **authenticated user + API access** on a **tenant subdomain**. **RBAC** (STEP 1.6, [rbac.md](../features/rbac.md)) adds `tenantId`, `workspaceId`, role, and `permissions[]` to JWT/session before dashboard shell. **Subdomain tenancy** ([004-subdomain-multi-tenancy.md](./004-subdomain-multi-tenancy.md)) defines how host and workspace align. Workspace settings UI (member invites) extends membership on top of that schema.

## Consequences

- Login and `(auth)` routes are built **before** `(dashboard)` shell (roadmap STEP 1.5).
- E2E and local dev use documented test credentials or `NEXT_PUBLIC_CANDIDATES_API=mock` only where explicitly allowed; production paths require auth.
- Server `.env` and client `.env.local` must share `AUTH_SECRET` (document in both `.env.example` files).
- Production uses per-tenant subdomains; configure Auth.js trusted hosts / `AUTH_URL` for wildcard subdomains ([004-subdomain-multi-tenancy.md](./004-subdomain-multi-tenancy.md)).

## Alternatives considered

- Nest-only login — rejected for this product; Auth.js gives faster session UX on Next 16.
- Clerk — rejected for now; team chose Auth.js + self-hosted Nest validation.

## References

- [auth.md](../features/auth.md)
- [workspace.md](../features/workspace.md) (session enrichment)
- [004-subdomain-multi-tenancy.md](./004-subdomain-multi-tenancy.md)
- [permissions.md](../architecture/permissions.md)
