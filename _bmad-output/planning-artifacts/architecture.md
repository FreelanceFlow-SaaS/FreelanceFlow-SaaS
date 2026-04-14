---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8]
workflowType: architecture
project_name: FreelanceFlow - SaaS
user_name: Dredjib
status: complete
lastStep: 8
completedAt: '2026-03-27'
lastUpdated: '2026-04-14'
inputDocuments:
  - _bmad-output/planning-artifacts/prd.md
  - docs/presentation.md
repositoryModel:
  split: true
  frontend:
    stack: Next.js
    note: 'Dedicated Git repository; verify exact Next.js version at init (see Starter section).'
  backend:
    stack: NestJS
    note: 'Dedicated Git repository; verify exact NestJS version at init (see Starter section).'
documentCounts:
  prd: 1
  uxDesign: 0
  research: 0
  projectDocs: 1
  projectContext: 0
---

# Architecture Decision Document — FreelanceFlow SaaS

_This document is the single source of truth for technical decisions for AI-assisted implementation. It assumes **two separate Git repositories**: a **Next.js** frontend and a **NestJS** backend, as agreed with the product owner._

---

## Project Context Analysis

### Requirements Overview

**Functional requirements (architectural view):**

- **Authentication:** registration and sign-in; all domain data scoped to the authenticated freelancer account.
- **Clients, prestations (services), invoices:** CRUD and workflows with **HT / TVA / TTC** computed and persisted; **invoice statuses** (draft, sent, paid, cancelled) with explicit transition rules.
- **PDF:** generate and download from **server-side** data so the PDF always matches persisted invoice state (PRD: no client-only “truth” for amounts).
- **API documentation:** required for evaluators (OpenAPI/Swagger or equivalent on the backend).

**Non-functional requirements:**

- **Compliance:** French-market invoicing rules as implemented in product scope; **EUR**; GDPR-aware handling of PII; document limitations in README.
- **Security:** HTTPS in production; **no cross-tenant access** — every query/command filtered by authenticated user/account.
- **Quality:** lint, Prettier, pre-commit; **mandatory backend unit tests**; load test with metrics on **staging**; CI on every relevant pipeline run.
- **Delivery:** Dockerfiles for frontend and backend; **docker-compose** (or equivalent) for local full stack; Git flow `feature/*` → `develop` → **`staging`** → `main`; tagged releases.

**Scale and complexity:**

- **Primary domain:** web SaaS — **browser UI (Next.js)** + **HTTP API (NestJS)** + **relational persistence**.
- **Complexity:** **medium** — bounded CRUD surface, but **VAT correctness**, **PDF parity**, **tenant isolation**, and **pipeline/staging gates** raise the bar.
- **Estimated architectural components:** auth module, client/service/invoice domains, PDF service, shared validation/DTO layer, Next.js app routes and feature UI, shared API client, infrastructure (DB, reverse proxy, CI).

### Technical Constraints and Dependencies

- **Split repositories** imply explicit **API contract** (versioned REST), **CORS**, and aligned **environment variables** (API URL, public vs server-only secrets).
- **State consistency:** invoice totals and PDF generation must use the **same backend services** that persist data.
- **No mandatory third-party integrations in V1** beyond hosting/registry choices. **Redis**, **managed Grafana Cloud**, and **transactional email APIs** are **optional** until the team enables those features; when enabled, they become **documented infrastructure dependencies** (env vars, runbook).

### Cross-Cutting Concerns

- **Tenant isolation** (user/account id on every domain operation).
- **Auditable money fields** (stored line amounts and tax breakdown, not only derived in the UI).
- **Error shape and validation** consistency between Nest and Next.
- **Observability** sufficient for staging/production debugging and course evaluation — **Grafana-centric stack** (see Observability decision).
- **Caching and async work** — **Redis** for bounded staleness / invalidation on invoice and list reads, and optional **job queues** for email and heavy PDF paths (see Redis decision).

---

## Starter Template Evaluation

### Primary Technology Domain

**Split-stack web SaaS:** **Next.js** (frontend repository) and **NestJS** (backend repository), aligned with the PRD and team structure (frontend / backend / DevOps).

### Starter Options Considered

| Area | Option | Notes |
|------|--------|--------|
| Frontend | `create-next-app` (official) | App Router, TypeScript, ESLint; add Prettier and testing as follow-up |
| Backend | `@nestjs/cli new` (official) | Modular structure, Jest default, easy Swagger module |

### Selected Starters

**Frontend — Next.js**

- **Rationale:** Official scaffold, active maintenance, fits SSR/SSG and App Router patterns for a French-market SaaS UI.
- **Version note (verify at project creation):** as of **2026-03-27**, the current stable line is **Next.js 16.2.x** (e.g. 16.2.1 per public release tags). **Always** run `create-next-app@latest` and pin the resolved version in `package.json`.

```bash
npx create-next-app@latest freelanceflow-frontend --typescript --eslint --app --src-dir --import-alias "@/*"
# Add Tailwind or CSS approach per team choice; align with Implementation Patterns.
```

**Backend — NestJS**

- **Rationale:** Opinionated modules, DI, OpenAPI integration, test-friendly structure — matches “mandatory backend unit tests” and API documentation requirements.
- **Version note:** as of **2026-03-27**, the **11.1.x** line is current stable (e.g. **11.1.17** per release history). Initialize with `@nestjs/cli@latest` and pin the resolved version.

```bash
npm i -g @nestjs/cli@latest
nest new freelanceflow-api --package-manager npm --strict
```

**Architectural decisions already implied by these starters**

- **Language:** TypeScript on both sides.
- **Frontend:** React via Next.js; file-based routing under `src/app`.
- **Backend:** Module/feature layout under `src/`; Nest testing defaults (Jest) unless the team standardizes on another runner later.
- **First implementation story:** initialize both repositories with the commands above (or equivalent package manager), then add shared quality tooling (Prettier, pre-commit) per PRD.

---

## Core Architectural Decisions

### Decision Priority Analysis

**Critical (block implementation if undefined):**

- API style and versioning; auth mechanism; database and ORM; where PDF is generated; CORS and env strategy for two repos.

**Important:**

- DTO validation library; error response format; date/decimal handling for money and VAT.  
- **Redis** availability when caching, rate limits, or queues are enabled; **cache invalidation** contract for invoices and lists (**FR43** / **NFR-C1**).  
- **Grafana** (or self-hosted LGTM) endpoints and credentials for staging/production; **OpenTelemetry** export configuration.

**Deferred (post-MVP):**

- Multi-seat orgs, billing tiers, external accounting/e-invoicing integrations.

### Repository and Integration Model

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Repositories | **Two Git repos** (frontend Next.js, backend NestJS) | Explicit ownership, separate CI pipelines, matches user direction |
| API style | **REST**, prefix **`/api/v1`** | PRD “REST (or equivalent)”; simple for graders and clients |
| Contract | **OpenAPI** generated from Nest (Swagger) | API documentation requirement |
| CORS | Allow **frontend origin(s)** only; credentials policy aligned with cookie/JWT choice | Security for split deployment |

### Data Architecture

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Database | **PostgreSQL** | Strong consistency for invoices and referential integrity |
| ORM / migrations | **Prisma** (recommended) or **TypeORM** — **pick one team-wide** | Prisma: ergonomic migrations and types; TypeORM: closer to classic Nest examples |
| Money / tax | Store **numeric/decimal** types (DB) and **minor units or fixed precision** rules in domain layer; never `float` for money | Correct HT/TVA/TTC and auditability |
| Tenant scoping | **Column:** `userId` (or `accountId`) on all tenant-owned rows; **every** query includes this filter | PRD isolation |

### Authentication and Security

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Auth model | **JWT access token** (short-lived) + **refresh token** (httpOnly cookie **or** secure storage strategy documented by team) | Stateless API; standard for SPA + mobile-ready |
| Passwords | **bcrypt** (or Argon2) via Nest; strong password policy | Baseline security |
| Transport | **HTTPS** everywhere in staging/production | PRD / SaaS baseline |
| Authorization | **Guards** on every controller; **no** relying on UI to hide IDs — validate resource ownership in services | Prevent cross-tenant leakage |

### API and Communication

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Request/response JSON | **camelCase** in JSON; DB columns **snake_case** if using Prisma `@map` | Consistency for Next.js/TS clients |
| Errors | Structured body: `statusCode`, `message`, `code` (optional), `details` for validation | Predictable UI error handling |
| Idempotency | Document for **PDF generation** and payment-related future endpoints | Future-safe pattern |
| Next ↔ Nest | Next.js uses **server-side** `fetch` to Nest for SSR where useful; browser calls Nest **directly** with CORS or via **Next Route Handlers** as BFF — **team picks one pattern** and documents it in README | Two valid models; avoid mixing without documentation |

### Frontend Architecture (Next.js)

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Routing | **App Router** under `src/app` | Starter default |
| Data fetching | Prefer **server components** for read-heavy pages; **client** for interactive forms; single **API client module** (typed) calling Nest | Clear boundaries |
| State | **React state + server data** for V1; avoid global store unless complexity demands | PRD scope |
| Auth UX | Protected routes via **middleware** or layout checks + token availability | Aligns with JWT model |

### PDF Generation

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Where | **NestJS** service (e.g. HTML template → **Puppeteer** or **PDFKit** / similar) | Single source of truth; heavy work off the Next process |
| Input | Read **persisted** invoice graph by id **and** `userId` | Tamper resistance and parity with UI |

### Infrastructure and Deployment

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Containers | **Dockerfile** in **each** repository | PRD |
| Local orchestration | **docker-compose** at DevOps discretion: either a third “infra” repo, or compose files documented to run **both** images with a shared network | Two-repo reality |
| CI/CD | Separate pipelines per repo: install → lint → format check → test → build → Docker build → publish → deploy to **staging** / **prod** | PRD |
| Environments | `staging` and `production`; load tests run against **staging** with **recorded metrics** | PRD journey (Léa) |

### Redis: caching, rate limiting, and job queues

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Technology | **Redis** (connection via `REDIS_URL` in backend `.env.example`) | Single well-supported component for cache, rate limits, and BullMQ-style queues |
| Rate limiting | Per-`userId` (and route) limits on sensitive endpoints (**login**, **PDF**, future **send email**) | Protects infra from abuse; aligns with SaaS fairness |
| Job queues | **BullMQ** (or equivalent) on Redis for **async email** and optional **async PDF** | Avoids blocking HTTP; retries and failure visibility |
| Session / JWT extras | Optional: refresh rotation metadata or token blocklist in Redis — **document if adopted** | Keeps stateless JWT story unless team explicitly extends |

### Invoice and list caching (PRD-aligned)

**Product vocabulary vs PRD:** business language such as **“validated”** / **“issued”** maps to PRD statuses **`sent`**, **`paid`**, or **`cancelled`** — not **`draft`**. Until a separate `validated` status exists in the schema, treat **`sent` and above** as the **cache-eligible** band for **read-heavy invoice payloads** (detail DTO, precomputed read models).

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **Draft (`draft`)** | **Do not** rely on long-lived cache for invoice totals or line items; at most **very short TTL** or **no cache** for invoice-by-id | Lines and HT/TVA/TTC change often; incorrect cache violates PRD integrity |
| **Stable band (`sent`, `paid`, `cancelled`)** | **Allow** cache-aside for **GET invoice by id** (tenant-scoped key) and related **list/dashboard slices** after successful Postgres commit | Fewer redundant reads under load; matches PRD intent for “cached or equivalent” optimizations |
| **Warm / populate** | On status transition **into** `sent` (or first read after transition), **write** cache entry **after** transaction commit, **or** **invalidate** list/dashboard keys only and let next read refill | Guarantees post-transition reads see new truth |
| **Invalidation** | On **any** mutation that affects aggregates: line edits (if allowed by rules), **any status transition**, client changes affecting displayed invoice — **delete** `user:{id}:invoice:{invoiceId}` and **invalidate** `user:{id}:dashboard` / list key families documented per endpoint class | PRD **FR43** / **NFR-C1**: correctness after writes; prefer **read-your-writes** for the acting user |
| **Staleness budget** | Team-doc default per PRD: **≤ 60 s** for non-actor readers unless invalidation fires first; **stricter** for the user who performed the write (**invalidate or immediate cache update**) | Bounded eventual consistency |
| **PDF bytes** | **Do not** store full PDF binaries in Redis; cache **metadata** (e.g. `updatedAt` / content hash) if needed; generate stream from **persisted** invoice | Memory, eviction, and single source of truth |

**Key naming (example):** `user:{userId}:invoice:{invoiceId}` for detail; `user:{userId}:invoices:list:{cursorOrHash}` for paginated lists — **always** include tenant id in the key.

### Observability (Grafana stack)

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Primary stack | **Grafana** as the **visualization and alerting** layer; backends **Prometheus** (metrics), **Loki** (logs), **Tempo** (traces) — “**LGTM**” pattern | Industry-standard OSS; **lower entry cost** than a full managed Elastic Observability footprint for typical student / MVP traffic |
| Managed vs self-hosted | **Prefer Grafana Cloud** for fastest path (free tier subject to vendor quotas); **alternative:** docker-compose **self-hosted** LGTM on existing VPS for **license cost €0** | Team picks one and documents URLs and retention in README/runbook |
| Instrumentation | **OpenTelemetry** in **NestJS** first (traces + metrics hooks); structured **JSON logs** (level, `requestId`, route, `statusCode`) shipped to **Loki** | Vendor-neutral export; PRD **V2** asks observability to be **documented for operators** |
| Elastic | **Not** the default observability vendor for this project | Cost/TCO and operational simplicity favor Grafana for this scope; re-evaluate only if enterprise mandates Elastic |
| PII / GDPR | **No** secrets, tokens, or full invoice line payloads in logs; **minimize** raw email; if `userId` appears, follow team redaction policy | Compliance and safe debugging |

### Email delivery and attachments

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Transport | **Development:** `SMTP_*` variables already sketched in backend `.env.example`. **Production:** prefer a **transactional email API** (e.g. Resend, Postmark, Brevo, SES) for deliverability, bounces, and quotas | SMTP alone is fragile at scale |
| Nest integration | **`@nestjs-modules/mailer`** (Nodemailer) for SMTP **or** thin HTTP client for provider API — **one** approach per environment, documented | Predictable configuration |
| Attachments (invoice PDF) | Prefer **generating from persisted invoice** in Nest, then **stream** to mail transport **or** attach from **short-lived temp file** deleted after send; respect provider **size limits** | Legal/audit parity with PDF decision |
| Alternative to large attachments | Email body with **time-limited signed URL** to `GET /api/v1/invoices/:id/pdf` (auth) reduces spam-filter issues | Product trade-off: link vs attachment |
| Async sending | **Queue** (BullMQ + Redis): API enqueues “send invoice email” job; worker sends mail, records success/failure for support | Non-blocking requests; retries |

### Decision Impact and Implementation Sequence

1. Bootstrap Nest + DB + Prisma/TypeORM + auth + OpenAPI.  
2. Implement domain modules (clients, services, invoices) with tenant guards.  
3. Add **Redis** client module; implement **cache + invalidation** hooks on invoice status and mutations per **Invoice and list caching** table; add **rate limiting** on sensitive routes.  
4. Add PDF pipeline and integration tests for VAT and parity.  
5. Add **mail + queue** modules when outbound email ships (V2 or earlier if scoped); never attach PDFs not sourced from persisted invoice rows.  
6. Bootstrap Next.js; implement auth UX and feature pages against `/api/v1`.  
7. Wire Docker and compose (include **Redis** where applicable); add **OTel + log JSON** config; document **Grafana** datasource URLs and dashboards in README/runbook.  
8. CI/CD and staging promotion; load tests still target **staging** with metrics recorded (can include Redis and mail worker health).

---

## Implementation Patterns and Consistency Rules

### Naming Patterns

**Database (PostgreSQL):**

- Tables: **snake_case**, plural where natural (`invoices`, `clients`).
- Primary keys: `id` (UUID or bigint — **choose one** and keep consistent).
- Foreign keys: `{entity}_id` (e.g. `user_id`, `client_id`).

**REST API:**

- Resource paths: **plural** nouns: `/api/v1/clients`, `/api/v1/invoices`.
- Route parameters: `:id` in Nest docs; URLs stable and lowercase.

**TypeScript / React:**

- Files: **kebab-case** for routes; **PascalCase** for React components.
- Functions/variables: **camelCase**.

### Structure Patterns

**Backend (NestJS):**

- Feature modules: `src/modules/<feature>/` with `controller`, `service`, `dto`, optional `entities` or Prisma usage.
- Shared: `src/common/` (guards, pipes, filters, interceptors).
- Cross-cutting: optional `src/modules/redis/` or `src/common/cache/` (Redis client + cache helpers); `src/modules/mail/` + `src/modules/queue/` when email/jobs ship; telemetry bootstrap next to `main.ts` (OpenTelemetry).

**Frontend (Next.js):**

- `src/app/` — routes and layouts.
- `src/components/ui/` — generic UI; `src/components/features/<domain>/` — domain components.
- `src/lib/api/` — typed fetch wrapper, base URL from env.

**Tests:**

- Backend: `*.spec.ts` **next to** source files (Nest default) unless team moves to `/test` — **be consistent**.
- Frontend: colocate `*.test.tsx` or use `__tests__` — **pick one** per repo.

### Format Patterns

- **JSON:** camelCase; **dates in API:** ISO-8601 strings in **UTC**; display localization in Next for FR users.
- **HTTP errors:** Nest **exception filter** maps to unified JSON error shape.
- **Success responses:** direct resource DTO or `{ data: T }` — **choose one**; this document recommends **direct DTO** for simplicity unless pagination wrapper needed (`{ data, meta }`).

### Process Patterns

- **Validation:** `class-validator` + DTOs on Nest inputs; mirror constraints in Zod on Next only if doing client-side preview — **server remains authoritative**.
- **Loading/errors:** Next client components use explicit `isLoading` / `error` state; map API codes to French user messages in UI layer.
- **Logging:** structured **JSON** logs in Nest (request id, route, `statusCode`, `userId` only per redaction policy); **no** secrets, tokens, or full invoice payloads; format chosen for **Loki** ingestion (Grafana stack).

### Enforcement (for all implementers / AI agents)

- **MUST** enforce `userId` / tenant filter on every persistence query for tenant-owned data.
- **MUST** use the same error JSON shape for all Nest HTTP errors.
- **MUST** generate PDF only from persisted invoice data via backend services.
- **MUST NOT** use binary floating point for persisted monetary amounts.
- **MUST** invalidate or update Redis cache keys affected by any invoice **create/update/delete** or **status transition** before returning success to the client (or apply documented read-your-writes strategy per **NFR-C1**).
- **MUST NOT** cache **`draft`** invoice financial snapshots with long TTL; stable-band caching rules in **Invoice and list caching** apply.
- **MUST NOT** store full **PDF binaries** in Redis; PDFs flow from persisted data at generation time.

**Good example:** `GET /api/v1/invoices/:id` returns 404 if the invoice belongs to another user.  
**Anti-pattern:** trusting `clientId` from the body without checking it belongs to the current user.  
**Anti-pattern:** serving a cached invoice detail after a status change without invalidation, causing stale HT/TVA/TTC.

---

## Project Structure and Boundaries

### Frontend repository (`freelanceflow-frontend` — example name)

```
freelanceflow-frontend/
├── README.md
├── package.json
├── next.config.ts
├── tsconfig.json
├── eslint.config.mjs
├── .env.example
├── .gitignore
├── Dockerfile
├── .github/
│   └── workflows/
│       └── ci.yml
├── public/
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── (auth)/
│   │   ├── (dashboard)/
│   │   └── api/                    # optional Route Handlers (BFF)
│   ├── components/
│   │   ├── ui/
│   │   └── features/
│   │       ├── clients/
│   │       ├── services/
│   │       └── invoices/
│   └── lib/
│       ├── api/
│       │   ├── client.ts
│       │   └── types.ts
│       └── auth/
└── e2e/                            # optional Playwright
```

### Backend repository (`freelanceflow-api` — example name)

```
freelanceflow-api/
├── README.md
├── package.json
├── nest-cli.json
├── tsconfig.json
├── .env.example
├── .gitignore
├── Dockerfile
├── .github/
│   └── workflows/
│       └── ci.yml
├── prisma/                         # if using Prisma
│   ├── schema.prisma
│   └── migrations/
├── src/
│   ├── main.ts
│   ├── app.module.ts
│   ├── common/
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── pipes/
│   └── modules/
│       ├── auth/
│       ├── users/
│       ├── clients/
│       ├── services/
│       ├── invoices/
│       ├── pdf/
│       ├── mail/                   # optional: transactional email
│       └── queue/                  # optional: BullMQ processors (email, etc.)
└── test/                           # e2e tests
```

### Architectural Boundaries

- **API boundary:** External world talks only to **Nest** on `/api/v1/*` (plus health/docs).
- **Auth boundary:** JWT validation in Nest guards; Next never trusts client-calculated totals for persistence.
- **Data boundary:** PostgreSQL accessed only from Nest (no direct DB access from Next).

### Requirements-to-Structure Mapping

| PRD area | Primary location |
|----------|------------------|
| Auth | `src/modules/auth/` (Nest); `src/lib/auth/`, `src/app/(auth)/` (Next) |
| Clients | `modules/clients/` (Nest); `components/features/clients/` (Next) |
| Prestations | `modules/services/` (Nest); `components/features/services/` (Next) |
| Invoices + VAT | `modules/invoices/` (Nest); `components/features/invoices/` (Next) |
| PDF | `modules/pdf/` (Nest) |
| OpenAPI | `main.ts` Swagger setup + DTO decorators |
| Redis / cache | `common/cache/` or `modules/redis/` (Nest); invalidation from `modules/invoices/` |
| Outbound email + attachments | `modules/mail/` + `modules/queue/` (Nest); env: `SMTP_*` and/or provider API keys |
| Observability | OTel + JSON logs in Nest; Grafana (Loki/Tempo/Prometheus) — **no** app code dependency on Grafana UI libraries |

### Integration and Data Flow

1. User signs in via Next → Nest `POST /api/v1/auth/login` → JWT returned.  
2. Next stores/refreshes token per chosen strategy.  
3. CRUD flows call Nest; Nest validates DTOs, applies tenant scope, persists.  
4. PDF: Next triggers `GET` or `POST /api/v1/invoices/:id/pdf` → Nest loads invoice, renders PDF stream.  
5. Optional email: user action → Nest persists state → **enqueue** job → worker loads **persisted** invoice, builds PDF if needed, sends via **mail** module (attachment or signed link per product choice).

---

## Architecture Validation Results

### Coherence

- **Next + Nest + PostgreSQL** are a common, compatible stack; versions should be pinned at bootstrap (`@latest` today, lockfile tomorrow).
- **Tenant isolation** is enforced at the service/repository layer in Nest, matching SaaS requirements.
- **PDF on Nest** aligns with “server-side truth” and UI parity.
- **Redis + Nest** is a standard pairing for cache and BullMQ; rules keep **money fields** authoritative in Postgres.
- **Grafana LGTM + OTel** is a coherent, cost-conscious observability path without binding domain code to a single commercial APM UI.

### Requirements Coverage

- **Functional:** Auth, clients, services, invoices, statuses, PDF, API docs — all mapped to modules and routes.
- **NFR:** CI/CD, Docker, tests, staging load test, GDPR awareness — addressed in decisions and patterns.  
- **Caching (FR43 / NFR-C1):** Redis invalidation and stable-band invoice caching — documented under **Invoice and list caching**.  
- **Observability:** Grafana stack + OTel — documented under **Observability (Grafana stack)**.

### Implementation Readiness

- **High** for greenfield execution, provided the team fixes ORM choice (Prisma vs TypeORM) and Next↔Nest auth transport (direct CORS vs BFF) in README at kickoff.

### Gap Analysis

| Priority | Gap | Suggestion |
|----------|-----|------------|
| Important | Exact **SIRET/SIREN/RCS** fields in V1 | PRD asks explicit in/out — product decision, then reflect in schema and PDF |
| Important | **Status transition** matrix | Finalize in domain doc or story before coding Mehdi journey |
| Nice | Shared **OpenAPI-generated TypeScript client** | Optional codegen from Nest Swagger for stronger coupling |
| Resolved (2026-04-14) | **Observability vendor** | **Grafana** (LGTM / Grafana Cloud) chosen over Elastic for cost and ops fit — see dedicated section |
| Resolved (2026-04-14) | **Cache strategy for invoices** | **Redis** + rules for `draft` vs `sent`/`paid`/`cancelled` + invalidation — see **Invoice and list caching** |
| Resolved (2026-04-14) | **Email + PDF attachment** | **Mail module + queue**; persisted invoice as source; SMTP dev / transactional API prod — see **Email delivery and attachments** |

### Architecture Completeness Checklist

- [x] Project context analyzed against PRD  
- [x] Starter commands and version verification strategy documented  
- [x] Critical decisions: data, auth, API, PDF, deployment  
- [x] Redis, invoice cache invalidation, Grafana observability, email + attachments (2026-04-14 amendment)  
- [x] Naming, structure, error, and tenant patterns defined  
- [x] Two-repository directory trees and boundaries  
- [x] Validation and gaps recorded  

### Readiness Assessment

**Overall status:** **READY FOR IMPLEMENTATION**  
**Confidence:** **High** (pending product clarifications on legal identifiers and status rules).  

**First implementation priority:** Run the **Next.js** and **NestJS** initialization commands in **separate** repositories, add PostgreSQL and ORM, then implement **auth + tenant context** before domain CRUD.

---

## Workflow Completion

- **Completed at:** 2026-03-27  
- **Workflow type:** architecture  
- **Inputs used:** `prd.md`, `docs/presentation.md`  
- **UX design artifact:** none in `planning-artifacts` (optional follow-up: `bmad-create-ux-design` if you want UI specs).  
- **Post-completion amendment (2026-04-14):** Redis (cache, queues, rate limits), **invoice cache rules** (`draft` vs stable statuses), **Grafana** observability stack, **email + PDF attachments** — all merged into **Core Architectural Decisions** and related sections above.

For **what to do next** in BMad: you are past planning for architecture — typical next steps are **epics/stories** (`bmad-create-epics-and-stories`), **sprint planning** (`bmad-sprint-planning`), or **story implementation** (`bmad-dev-story` / `bmad-quick-dev`) using this document as the technical contract.
