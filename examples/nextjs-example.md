# Next.js SaaS — TokenSaver Example

> A lightweight agent-agnostic project context system for **SaaSAuth**, a multi-tenant SaaS with Next.js 14, Prisma, Stripe, and NextAuth.

---

## Generated Files

```
.tokensaver/
├── overview.md
├── architecture.md
├── codebase-map.md
├── conventions.md
├── decisions.md
├── gotchas.md
└── session-handoff.md

TOKENSAVER.md
```

---

## .tokensaver/overview.md

```markdown
# SaaSAuth — Overview

> Multi-tenant auth SaaS. B2B, team-based, SSO + RBAC.

**Last updated:** 2026-03-15

---

## What

Identity and access management as a service. Companies integrate our SDK for social login, SAML SSO, team management, RBAC, and audit logs.

## Tech Stack

| Layer | Tech |
|-------|------|
| Framework | Next.js 14 (App Router) |
| Language | TypeScript (strict) |
| Database | PostgreSQL via Prisma |
| Auth | NextAuth v5 |
| Payments | Stripe (subscriptions + usage) |
| UI | Tailwind + shadcn/ui |
| Jobs | Inngest |
| Hosting | Vercel + PlanetScale |

## Key URLs

- Prod: https://app.saasauth.dev
- Staging: https://staging.saasauth.dev
- Docs: https://docs.saasauth.dev

## Related
- [[codebase-map]] — where things live
- [[architecture]] — system design
```

---

## .tokensaver/codebase-map.md

```markdown
# SaaSAuth — Codebase Map

> Where everything lives. Each directory annotated.

**Last updated:** 2026-03-15

---

```
saasauth/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── (auth)/             # Login, register, SSO pages
│   │   ├── (dashboard)/        # Tenant dashboard
│   │   └── api/                # REST API + webhooks
│   ├── components/
│   │   ├── ui/                 # shadcn/ui primitives
│   │   └── shared/             # Nav, sidebar, modals
│   ├── lib/                    # Business logic
│   │   ├── auth/               # NextAuth config + callbacks
│   │   ├── billing/            # Stripe checkout, portal, webhooks
│   │   └── org/                # Tenant & team management
│   ├── server/                 # Server-only code
│   │   ├── db/                 # Prisma client
│   │   └── queues/             # Inngest job handlers
│   └── types/                  # Shared TypeScript types
├── prisma/
│   └── schema.prisma           # ~400 lines, single file
└── tests/
    ├── e2e/                    # Playwright (critical paths only)
    └── integration/            # API tests (vitest)
```

## Key Patterns

- **Server Components by default.** `'use client'` only for interactivity.
- **tRPC for internal API, REST for public.** Versioned at `/api/v1/`.
- **Tenant isolation via Prisma middleware** (PlanetScale has no RLS).
- **E2E tests cover:** Signup, login, subscription, team invite.

## Related
- [[conventions]] — coding style
- [[architecture]] — data flow
```

---

## .tokensaver/decisions.md

```markdown
# SaaSAuth — Decisions

> Key architectural choices.

**Last updated:** 2026-03-15

---

## ADR-001: tRPC for Internal API

**Date:** 2025-11-10 | **Status:** Accepted

**Context:** Need typesafe API between server and dashboard client.

**Decision:** tRPC for internal. REST for public API.

**Rationale:**
- End-to-end type safety without codegen
- Dashboard is colocated — no external consumers
- REST remains for public API consumers

**Consequences:** Two API styles. tRPC v11 migration planned Q3 2026.

---

## ADR-002: PlanetScale over RDS

**Date:** 2025-10-01 | **Status:** Accepted

**Context:** Production MySQL-compatible database.

**Decision:** PlanetScale with RDS as fallback.

**Rationale:**
- Serverless scaling for spiky usage
- Branching workflow (DB per branch)
- Built-in connection pooling

**Consequences:** No FK constraints (app-layer enforcement). Vendor lock-in.
```

---

## .tokensaver/gotchas.md

```markdown
# SaaSAuth — Gotchas

> Things that will surprise you.

**Last updated:** 2026-03-15

---

## Multi-Tenancy

- **Tenant isolation is middleware-based.** Raw SQL queries bypass it — always include `WHERE orgId = ?` manually.
- **Org context comes from JWT, not URL.** Always get org from session.

## Stripe Webhooks

- **Raw body required.** Next.js body parsing must be disabled for the webhook route. Signature verification needs the raw buffer.
- **Idempotency is critical.** Check `event.id` before processing.

## NextAuth

- **Custom Prisma adapter.** Official adapter doesn't support multi-tenant users. Check our adapter when upgrading NextAuth.
- **Keep JWT payload small.** Sessions include org + role data. Don't add user profile or audit info.

## Database

- **No foreign keys.** PlanetScale limitation. Integrity at app layer.
- **Online schema changes.** Add nullable columns first, backfill, then add defaults.

## Related
- [[architecture]] — system overview
```

---

## .tokensaver/session-handoff.md

```markdown
# SaaSAuth — Session Handoff

> What's in progress and what's next.

**Last updated:** 2026-03-15

---

## In Progress

- SAML SSO: certificate auto-rotation needs implementation. Currently manual.

## Next Steps

- tRPC v11 migration (planned Q3 2026)
- Usage-based billing: add overage alerting
- Audit log export feature (compliance requirement)

## Blockers

- None currently

## Last Session

Added SAML SSO support for enterprise customers (Okta, Azure AD). New `src/lib/auth/saml/` module. Certificates stored in DB, rotation endpoint created but not yet automated.
```

---

## Optional: AGENTS.md Adapter

If the project needs generic agent compatibility, add this thin adapter:

```markdown
# AGENTS.md

## TokenSaver
Read `TOKENSAVER.md` and `.tokensaver/` before scanning source code.
```

---

*This example shows a complete TokenSaver context system. Each context file is short enough to read in seconds, detailed enough to save thousands of tokens per session. `TOKENSAVER.md` is the universal entry point. Adapter files are optional.*
