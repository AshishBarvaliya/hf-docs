# Environment setup

How to configure **local development** and **hosted** environments for Hyreefy (`client/` + `server/`). Secrets never go in git — only `.env.example` templates are committed.

**Deployment pipeline:** [deployment.md](./deployment.md) · **Hosted stack detail:** [architecture/server/deployment-render-neon.md](./architecture/server/deployment-render-neon.md)

## Prerequisites

| Tool | Version | Used for |
|------|---------|----------|
| Node.js | 22+ | Client and server |
| Docker | current | Local PostgreSQL (`server/docker-compose.yml`) |
| Git | — | Separate repos under `client/` and `server/` |

Optional (Neon ops from your laptop): global [Neon CLI](https://neon.com/docs/reference/neon-cli) after `npm i -g neon@latest && neon auth`.

---

## Local development (recommended path)

### 1. Database (server only)

```bash
cd server
docker compose up -d
```

Postgres listens on `localhost:5432`, database `hiring_os`, user/password `postgres` / `postgres`.

### 2. Server env

```bash
cd server
cp .env.example .env
```

| Variable | Local value | Notes |
|----------|-------------|--------|
| `NODE_ENV` | `development` | |
| `PORT` | `3032` | API port |
| `DATABASE_URL` | `postgresql://postgres:postgres@localhost:5432/hiring_os` | **Always local Docker** — do not point at Neon for daily dev |
| `CORS_ORIGIN` | `http://localhost:3031` | Dev also allows `*.localhost` origins automatically |
| `APP_BASE_DOMAIN` | `localhost` | Enables wildcard tenant CORS in production when set |
| `APP_PUBLIC_ORIGIN` | `http://localhost:3031` | Apex URL for invite links (optional locally) |
| `AUTH_SECRET` | ≥ 32 characters | Must match client; example in `.env.example` is dev-only |

Then:

```bash
npm install --legacy-peer-deps
npm run db:migrate
npm run db:seed
npm run start:dev
```

Health: [http://localhost:3032/api/v1/health](http://localhost:3032/api/v1/health)

**Seed logins** (after `db:seed`): see comments in `server/.env.example`.

### 3. Client env

```bash
cd client
cp .env.example .env.local
```

| Variable | Local value | Notes |
|----------|-------------|--------|
| `NEXT_PUBLIC_API_ORIGIN` | `http://localhost:3032` | No trailing slash |
| `AUTH_SECRET` | Same string as server `AUTH_SECRET` | `openssl rand -base64 32` for a new secret |
| `AUTH_TRUST_HOST` | `true` | Keeps prod/preview behavior aligned |
| `APP_BASE_DOMAIN` | `localhost` | Tenant hosts: `http://acme.localhost:3031` |
| `KNOWN_TENANT_SLUGS` | `acme,beta` | Optional dev shortcut; **unset** on clean-sheet test envs (tenant gate uses API) |

Then:

```bash
npm install
npm run dev
```

App: [http://localhost:3031](http://localhost:3031) · Tenant example: [http://acme.localhost:3031](http://acme.localhost:3031)

### 4. Verify the pair

1. Open `http://acme.localhost:3031` and sign in with a seed user.
2. Confirm API calls hit `localhost:3032` (network tab) and candidates load when permitted.

---

## Optional: Neon credentials on your machine

Production data lives on Neon; local `.env` should stay on Docker. For one-off remote migrations or inspection:

```bash
cd server
npm run db:env:neon          # writes .env.neon.local (gitignored)
npm run db:migrate:neon      # runs drizzle-kit migrate using unpooled URL
```

Never commit `.env`, `.env.local`, or `.env.neon.local`.

---

## Production & preview (hosted)

Set variables in each host’s dashboard — **not** in the repo.

### Shared rule: `AUTH_SECRET`

Generate once and use the **identical** value on:

- Render → `AUTH_SECRET`
- Vercel → `AUTH_SECRET`

```bash
openssl rand -base64 32
```

### Server (Render)

Copy from Neon **production** branch → **Connect** (pooled + direct URLs).

| Variable | Required | Purpose |
|----------|----------|---------|
| `NODE_ENV` | yes | `production` |
| `PORT` | yes | `10000` (Render Node default) |
| `DATABASE_URL` | yes | Neon **pooled** connection string — app runtime |
| `DATABASE_URL_UNPOOLED` | yes | Neon **direct** string — `preDeployCommand` migrations |
| `AUTH_SECRET` | yes | Session/JWT alignment with client |
| `CORS_ORIGIN` | yes | Vercel origin(s), e.g. `https://app.example.com` (comma-separate previews if needed) |

Validated in `server/src/config/env.ts`.

### Client (Vercel)

| Variable | Required | Purpose |
|----------|----------|---------|
| `NEXT_PUBLIC_API_ORIGIN` | yes | Render API URL, e.g. `https://hyreefy-api.onrender.com` |
| `AUTH_SECRET` | yes | Same as server |
| `AUTH_TRUST_HOST` | yes | `true` |
| `APP_BASE_DOMAIN` | yes (prod) | Real apex domain for `{slug}.example.com` |
| `KNOWN_TENANT_SLUGS` | prod | Comma-separated provisioned slugs; empty fails closed |
| `AUTH_URL` | sometimes | Set if Auth.js canonical URL must differ from request host |

Apply to **Production**; duplicate or adjust for **Preview** if previews use a different API or domain.

### CI (GitHub Actions)

Workflows inject minimal env for tests — no Neon secrets. Server CI uses ephemeral Postgres; see `server/.github/workflows/ci.yml` and `client/.github/workflows/ci.yml`.

---

## Troubleshooting

| Symptom | Check |
|---------|--------|
| Login works locally but not on Vercel | `AUTH_SECRET` matches server; `NEXT_PUBLIC_API_ORIGIN` points at Render |
| CORS errors in browser | Render `CORS_ORIGIN` includes the exact browser origin (scheme + host) |
| Migrations fail on Render | `DATABASE_URL_UNPOOLED` set (not pooler hostname) |
| `tenant-not-found` on apex URL | Set `APP_BASE_DOMAIN` to your Vercel host (or rely on `*.vercel.app` auto-detect); set `KNOWN_TENANT_SLUGS` |
| Global login missing workspace field | Open the apex URL (`https://your-project.vercel.app`), not an unknown subdomain |
| Server won’t start | `AUTH_SECRET` length ≥ 32 |

---

## File reference

| Path | Committed? | Role |
|------|------------|------|
| `server/.env.example` | yes | Server template |
| `server/.env` | no | Local server secrets |
| `server/.env.neon.local` | no | Pulled Neon URLs for optional remote ops |
| `client/.env.example` | yes | Client template |
| `client/.env.local` | no | Local client secrets |
