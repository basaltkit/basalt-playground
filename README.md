# Basalt playground

A small, **runnable** [Basalt](https://basaltkit-docs.pages.dev) app — a
multi-tenant, typed JSON API on Fastify, with events + an audit listener. It uses
the **published `@basaltkit/*` packages** (no monorepo needed), so it boots in a
browser WebContainer.

[![Open in StackBlitz](https://developer.stackblitz.com/img/open_in_stackblitz.svg)](https://stackblitz.com/github/basaltkit/basalt-playground)

## Run it

```bash
npm install
npm run dev        # → http://localhost:3000
```

## Try it

```bash
curl localhost:3000/                                  # welcome + endpoint list
curl localhost:3000/health

# Multi-tenancy: the same code, isolated per tenant. Pick a tenant with a header.
curl -H 'x-tenant-id: acme'   -X POST localhost:3000/projects -d '{"name":"Acme site"}' -H 'content-type: application/json'
curl -H 'x-tenant-id: globex' -X POST localhost:3000/projects -d '{"name":"Globex app"}' -H 'content-type: application/json'

curl -H 'x-tenant-id: acme'   localhost:3000/projects   # only Acme's projects
curl -H 'x-tenant-id: globex' localhost:3000/projects   # only Globex's projects
```

Each request resolves a tenant (header or subdomain), and the repository
partitions data by `ctx().tenant` — the emitter never passes a tenant id; it
rides the async context. That's the Basalt multi-tenancy model in ~100 lines.

## What's inside

- `src/app.ts` — the app: config, logger, events, **tenancy**, a domain plugin, Fastify.
- `src/routes.ts` — typed routes (Zod in/out) via `@basaltkit/fastify`.
- `src/domain.ts` — a tenant-scoped in-memory repository + domain events.

Seeded tenants: `acme`, `globex`. Swap `MemoryTenantSource` for your database and
the in-memory repo for a Prisma client extended with `@basaltkit/prisma`.

## License

MIT
