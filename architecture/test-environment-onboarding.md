# Isolated test environment (tenant onboarding)

Use a **non-production apex domain** (placeholder: `your-test-apex.example`) with tenant hosts `{slug}.your-test-apex.example`. Do **not** commit the real domain or secrets.

## Stack (recommended)

| Layer | Isolation |
|-------|-----------|
| **Vercel** | Separate project or preview branch deployment for the client |
| **API** | Separate Render (or local) service + env |
| **Database** | Neon branch or database; **migrations only** — no `npm run db:seed` |
| **DNS** | Cloudflare on the unused domain |

## Environment variables

### Client (Vercel)

| Variable | Example placeholder |
|----------|---------------------|
| `APP_BASE_DOMAIN` | `your-test-apex.example` |
| `NEXT_PUBLIC_API_ORIGIN` | `https://your-test-api.example` |
| `AUTH_SECRET` | shared 32+ char secret |
| `AUTH_TRUST_HOST` | `true` |
| `KNOWN_TENANT_SLUGS` | **unset** for clean-sheet tests |

Do **not** set `AUTH_URL` to a single subdomain.

### API

| Variable | Example placeholder |
|----------|---------------------|
| `APP_BASE_DOMAIN` | same as client |
| `APP_PUBLIC_ORIGIN` | `https://your-test-apex.example` |
| `CORS_ORIGIN` | `https://your-test-apex.example` |
| `AUTH_SECRET` | same as client |
| `DATABASE_URL` | Neon pooled URL for test branch |

## Cloudflare + Vercel DNS (wildcard tenants)

1. In **Vercel** → Project → **Domains**: add apex `your-test-apex.example` and wildcard `*.your-test-apex.example`.
2. In **Cloudflare** DNS (DNS only / grey cloud for proxied records per [Vercel troubleshooting](https://vercel.com/docs/domains/troubleshooting)):
   - Apex: `A` or `CNAME` as Vercel instructs (`vercel domains inspect`).
   - Wildcard: `CNAME` `*` → `cname.vercel-dns-0.com` (or value from inspect).
   - For wildcard TLS with external DNS: delegate `_acme-challenge` NS to `ns1.vercel-dns.com` / `ns2.vercel-dns.com` per [Vercel wildcard external DNS](https://vercel.com/docs/domains/working-with-domains/add-a-domain#use-wildcard-domains-with-an-external-dns-provider).
3. Wait for Vercel **Valid Configuration** on both apex and wildcard.

## Bootstrap database

```bash
cd server
npm run db:migrate
# Do not run db:seed on this environment
```

## Smoke test (clean sheet)

1. Open `https://your-test-apex.example/signup`.
2. Register with a new email and slug `demo-{random}`.
3. Confirm redirect to `https://demo-{random}.your-test-apex.example/overview` with session.
4. Open `https://not-real.your-test-apex.example` → tenant-not-found.

## Reset / teardown

- **Database:** reset Neon branch from parent or drop and re-run migrations.
- **Vercel:** remove test domains or delete test project.
- **Cloudflare:** remove test DNS records when done.

See also [deployment.md](../deployment.md) and [setup-environment.md](../setup-environment.md).
