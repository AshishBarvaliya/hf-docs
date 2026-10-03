# Client domain — onboarding

Mirrors apex signup and tenant handoff UI.

## Routes

| Path | Host | Purpose |
|------|------|---------|
| `/signup` | Apex | Register + choose tenant slug |
| `/auth/handoff` | Tenant | Exchange handoff token → Auth.js session |
| `/login` | Apex or tenant | Existing login |

## Libraries

- `src/lib/auth/register.ts` — `POST /api/v1/auth/register`
- `src/lib/auth/handoff.ts` — `POST /api/v1/auth/handoff`
- `src/lib/tenant/resolve-tenant.ts` — `GET /api/v1/tenants/:slug/status` for proxy gate

## Flow

```text
apex /signup → API register → sessionStorage handoffToken
  → redirect to https://{slug}.{APP_BASE_DOMAIN}/auth/handoff
  → signIn(handoff) → /overview
```

## Related

- [onboarding.md](../../../features/onboarding.md)
