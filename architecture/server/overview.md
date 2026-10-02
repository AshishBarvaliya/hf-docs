# Hyreefy — server architecture overview

The server is the **orchestration API** for the Hyreefy client. It owns **tenant and workspace** data, ATS entities, workflows, and permission enforcement. All domain queries are scoped by the authenticated **workspace** (and thus tenant). Assessment runtime, proctoring, meetings, and email delivery remain external products (see [`../../adr/001-orchestration-layer.md`](../../adr/001-orchestration-layer.md)).

**Multi-tenant model:** [multi-tenancy.md](../multi-tenancy.md), [ADR 004](../../adr/004-subdomain-multi-tenancy.md).

**Production (AWS):** [aws-platform.md](../aws-platform.md), [deployment-aws.md](./deployment-aws.md). Long-running AI and report generation use [async-jobs.md](../async-jobs.md) and standalone SQS workers ([ADR 006](../../adr/006-async-ai-workers.md))—not the HTTP request thread.

**How we ship:** [`.cursor/rules/product-delivery-principles.mdc`](../../.cursor/rules/product-delivery-principles.mdc) — dependency-ordered features, one vertical slice at a time, client and server together, production-ready (auth, validation, permissions, tests) per slice.

## Stack

| Layer | Choice |
|-------|--------|
| Framework | NestJS 12, TypeScript (ESM) |
| Database | PostgreSQL |
| ORM | Drizzle |
| Validation | Zod at HTTP boundaries (aligned with client schemas) |
| Tests | Vitest (unit + e2e) |

## Code organization

- **`src/domains/<name>/`** — Nest module per business area (`controller`, `service`, `*.schemas.ts`).
- **`src/database/`** — Drizzle schema, `DatabaseModule`, migrations output in `drizzle/`.
- **`src/config/`** — Env validation (`env.ts`).
- **`src/health/`** — Liveness/readiness endpoints.

Thin `AppModule` wires global config, database, and domain modules only.

## API conventions

- Base path: `/api/v1`
- JSON responses; validate outbound payloads with Zod where types are shared with the client.
- CORS: `CORS_ORIGIN` or pattern for tenant subdomains (default dev: `http://*.localhost:3000` and `http://localhost:3000` during spike migration).
- Default port: **3001** (client uses 3000).

## First endpoints

| Method | Path | Purpose |
|--------|------|---------|
| GET | `/api/v1/health` | Health check (public) |
| POST | `/api/v1/auth/login` | Tenant-scoped login; JWT `permissions[]` from the workspace role |
| GET | `/api/v1/candidates` | Bearer + `candidates.read`; list candidates for the token workspace |

## Local database

```bash
docker compose up -d
cp .env.example .env
npm run db:migrate
npm run db:seed
npm run start:dev
```
