# Synch API

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/hjinco/synch/tree/main/apps/api)

Cloudflare Worker API for Synch. Public self-hosting is supported through the Deploy to Cloudflare button, which uses the `self-hosted` Wrangler environment. Synch managed deploys use the `managed` Wrangler environment in the same config file.

## Required Secrets

Set these during Deploy to Cloudflare or local development:

- `BETTER_AUTH_SECRET`: random secret for Better Auth session signing.
- `SYNC_TOKEN_SECRET`: separate random secret for sync websocket token signing.

Generate values with:

```sh
openssl rand -hex 32
```

Managed deployments and local development with `SELF_HOSTED=false` also require:

- `AUTH_EMAIL_FROM`: Cloudflare Email Service verified sender address, for example `Synch <noreply@example.com>`.
- An Email Service send binding named `EMAIL`. Production Wrangler config includes this binding; enable Email Service for the sender domain before deploying.

## Local Development

```sh
pnpm install
pnpm db:migrate:local
pnpm dev
```

Local development uses `env.managed`. The `self-hosted` environment is kept for Deploy to Cloudflare flows.

For public self-hosting, use the Deploy to Cloudflare button at the top of this README. The deploy command runs:

```sh
pnpm deploy
```

That command first deploys through `env.self-hosted` so Wrangler can provision configured resources, then applies D1 migrations to the provisioned `DB` binding. The `self-hosted` environment intentionally omits generated resource IDs for auto-provisioned resources.

For Synch's managed deployment path, use:

```sh
pnpm deploy:managed
```

That command uses `env.managed`, applies D1 migrations to the managed D1 database, then replaces the Worker.

## GitHub Actions

API CI runs on API-related pull requests and pushes to `main`:

```sh
pnpm -C apps/api typecheck
pnpm -C apps/api test:unit
pnpm -C apps/api test:integration
```

API deployment runs by manual GitHub Actions dispatch. It uses the managed deploy command:

```sh
pnpm -C apps/api deploy:managed
```

Configure these GitHub secrets for deployment:

- `CLOUDFLARE_API_TOKEN`
- `CLOUDFLARE_ACCOUNT_ID`
