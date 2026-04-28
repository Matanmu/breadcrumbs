# Acme Billing

SaaS billing portal — lets customers manage subscriptions, invoices, and
payment methods.

## Stack
- Node.js 22, TypeScript 5.4
- Next.js 15 (App Router)
- PostgreSQL 16 via Prisma
- Stripe API v2024-06-20

## Architecture
- Webhook-driven subscription state (not polling) — see `src/webhooks/`
- All API responses wrapped in `{ data, error, meta }` envelope
- Auth via Clerk; user IDs are Clerk IDs throughout (no internal user table)

## Conventions
- Feature flags via `src/flags.ts` — check before adding env-var conditionals
- DB migrations run automatically on deploy (`postinstall` → `prisma migrate deploy`)
