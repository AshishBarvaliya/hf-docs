# Sprint auth visual fidelity — progress checklist

**Sprint id:** `sprint-auth-visual-fidelity` (**closed** 2026-10-03)  
**Archives:** `client/archive/sprint-auth-visual-fidelity.yaml`, `server/archive/sprint-auth-visual-fidelity.yaml`  
**Goal:** Match auth, onboarding, tenant-not-found, and no-access UI to the canonical Hyreefy designs.  
**Scope:** Login default/error/expired/busy states; shared auth footer and theme shortcut; signup and handoff derived from the login system; tenant-not-found; reusable in-app no-access card; tenant display name from the existing status endpoint; responsive behavior and tests.  
**Non-goals:** Password reset backend, new legal/status pages, changes to the static `designs/` files, dashboard/product screens outside access states, new tenant/workspace columns, email verification, billing, SSO, or custom domains.

| Area | Done | Total | % |
|------|------|-------|---|
| Data / DB | 1 | 1 | 100% |
| Server | 3 | 3 | 100% |
| Client | 5 | 5 | 100% |
| Docs | 2 | 2 | 100% |
| **Overall** | **11** | **11** | **100%** |

## Data / DB

- [x] No migration — reuse existing `tenants.name`; auth visuals introduce no persisted fields

## Server

- [x] Tenant status contract returns existing tenant `name`
- [x] Server unit tests cover active tenant branding lookup and missing tenant
- [x] Server API e2e covers branded status response and 404

## Client

- [x] Shared auth layout matches mockup dimensions, card treatment, footer, and `D` theme shortcut
- [x] Login matches default, invalid credentials, session-expired, password visibility, and busy states
- [x] Signup/handoff use the derived auth visual system; tenant-not-found and no-access match their mockups
- [x] Client unit tests cover tenant status parsing/cache and extracted auth behavior
- [x] Playwright covers auth structure, failure/busy interactions, tenant-not-found, and no-access

## Docs

- [x] Auth/onboarding Dev sections document visual behavior, status response, data decision, and tests
- [x] Sprint YAML and checklist remain aligned through completion
