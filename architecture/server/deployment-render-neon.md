# Deployment — Render (API), Vercel (client), Neon (Postgres)

Hyreefy’s default hosted stack: **Neon** for production PostgreSQL, **Render** for the Nest API, **Vercel** for the Next.js client. Local development keeps **Docker Postgres** only (`server/docker-compose.yml`).

**Also see:** [deployment.md](../../deployment.md) (overview + checklist) · [setup-environment.md](../../setup-environment.md) (env tables for local and hosted).

## Overview

| Environment | Database | API | Client |
|-------------|----------|-----|--------|
| Local | `postgresql://postgres:postgres@localhost:5432/hiring_os` | `npm run start:dev` (:3001) | `npm run dev` (:3000) |
| Production | Neon branch `production` | Render Web Service | Vercel |
| Isolated test (onboarding) | Dedicated Neon branch (migrations only) | Separate Render service | Separate Vercel project + wildcard DNS |

Push-to-deploy: **Vercel** and **Render** redeploy on `main` (already connected). **GitHub Actions** in each repo runs lint/tests on PRs and pushes. **Schema** on Neon is applied on each Render deploy via `preDeployCommand: npm run db:migrate`.

## Neon (server repo)

The API repo is linked to Neon project `jolly-resonance-29162110`, branch **`production`**.

| File | Purpose |
|------|---------|
| `server/.neon` | Linked org/project/branch (gitignored) |
| `server/neon.ts` | IaC policy (`neon deploy` / `neon config apply`) |
| `server/.env.neon.local` | Pulled Neon credentials for **optional** remote ops (gitignored) |

**Local `.env` stays on localhost.** Do not point `server/.env` at Neon for day-to-day dev. To refresh remote credentials:

```bash
cd server
neon env pull --file .env.neon.local
```

Apply Drizzle migrations to Neon manually (e.g. first bootstrap):

```bash
cd server
npm run db:env:neon
npm run db:migrate:neon
```

`drizzle.config.ts` prefers `DATABASE_URL_UNPOOLED` for migrations (Neon direct connection); the Nest app at runtime uses pooled `DATABASE_URL`.

CLI setup (one-time per machine):

```bash
npm i -g neon@latest && neon auth
cd server
neon link --project-id jolly-resonance-29162110 --branch production -y
neon config init   # already done; edit neon.ts then:
neon deploy --no-env-pull --allow-protected --update-existing
```

`neon skills` requires Node **≥ 22.20**; upgrade Node locally if you want agent skills installed.

## Render (API)

Use `server/render.yaml` as a blueprint or mirror these settings on your Web Service:

| Setting | Value |
|---------|--------|
| Build | `npm ci --legacy-peer-deps && npm run build` |
| Start | `npm run start:prod` |
| Pre-deploy | `npm run db:migrate` |
| Health check | `/api/v1/health` |

**Environment variables** (set in Render dashboard — copy from Neon **production** branch → Connect):

| Variable | Source |
|----------|--------|
| `DATABASE_URL` | Neon **pooled** connection string |
| `DATABASE_URL_UNPOOLED` | Neon **direct** connection string (migrations) |
| `AUTH_SECRET` | Strong secret (≥ 32 chars); **same** as Vercel |
| `CORS_ORIGIN` | Apex app URL, e.g. `https://your-test-apex.example` |
| `APP_BASE_DOMAIN` | Hostname only, same as client `APP_BASE_DOMAIN` (enables `https://*.{domain}` CORS) |
| `APP_PUBLIC_ORIGIN` | Optional apex URL for invite links |
| `NODE_ENV` | `production` |
| `PORT` | `10000` (Render default for Node) |

Do not run `db:seed` against production unless you intend to load dev tenants.

## Vercel (client)

Vercel builds on push. Set **Environment variables** (Production + Preview as needed):

| Variable | Example / notes |
|----------|-----------------|
| `NEXT_PUBLIC_API_ORIGIN` | Render service URL, e.g. `https://hyreefy-api.onrender.com` |
| `AUTH_SECRET` | Same as Render `AUTH_SECRET` |
| `AUTH_TRUST_HOST` | `true` |
| `APP_BASE_DOMAIN` | Apex hostname for `{slug}.{domain}` (test or production) |
| `KNOWN_TENANT_SLUGS` | Optional dev shortcut; leave unset on clean-sheet test envs |

## CI (GitHub)

- `server/.github/workflows/ci.yml` — lint, unit tests, e2e with Postgres service, migrations.
- `client/.github/workflows/ci.yml` — lint, unit tests, `next build`.

No Neon secrets in CI; server e2e uses ephemeral Postgres on the runner.

## References

- [overview.md](./overview.md)
- [deployment-aws.md](./deployment-aws.md) (future AWS target)
- Neon + Drizzle: [Neon Drizzle migrations](https://neon.com/docs/guides/drizzle-migrations)
