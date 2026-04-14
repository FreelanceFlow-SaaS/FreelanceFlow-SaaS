---
stepsCompleted:
  - step-01-validate-prerequisites
  - step-02-design-epics
  - step-03-create-stories
  - step-04-final-validation
workflowStatus: complete
completedAt: '2026-04-14'
inputDocuments:
  - _bmad-output/planning-artifacts/prd.md
  - _bmad-output/planning-artifacts/architecture.md
  - _bmad-output/planning-artifacts/ux-design-specification.md
---

# FreelanceFlow - SaaS - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for FreelanceFlow - SaaS, decomposing the requirements from the PRD, UX Design if it exists, and Architecture requirements into implementable stories.

## Requirements Inventory

### Functional Requirements

```
FR1: A visitor can create an account to use the product.
FR2: A registered user can sign in.
FR3: An authenticated user can end their session (sign out) when the product provides that control.
FR4: An authenticated user can access only their own clients, services, and invoices (no cross-account access).
FR5: An authenticated user can maintain seller information used on invoices (identity and identifiers required by the implemented French-market rules in V1).
FR6: The product can include the seller's maintained information on invoices and PDFs when those fields are in scope for V1.
FR7: An authenticated user can create a client with name, email, company, and address.
FR8: An authenticated user can view a list of their clients.
FR9: An authenticated user can update an existing client's information.
FR10: An authenticated user can delete a client they no longer need.
FR11: An authenticated user can create a service with a title and an hourly rate.
FR12: An authenticated user can view a list of their services.
FR13: An authenticated user can update an existing service.
FR14: An authenticated user can delete a service.
FR15: An authenticated user can create an invoice associated with one client.
FR16: An authenticated user can add one or more lines to an invoice, referencing defined services and quantities or hours as required by the product rules.
FR17: An authenticated user can remove or adjust lines on an invoice when the invoice status allows editing.
FR18: The product can compute HT, VAT, and TTC from invoice lines and rates according to the French-market VAT rules implemented in V1.
FR19: An authenticated user can view HT, VAT, and TTC on the invoice before generating a PDF.
FR20: An authenticated user can set or transition invoice status among the supported values (draft, sent, paid, cancelled) according to the product's transition rules.
FR21: An authenticated user can view a list of their invoices.
FR22: An authenticated user can open and view the details of a single invoice.
FR23: Stored invoice monetary amounts remain consistent with the line items and applied VAT rules (single source of truth for totals).
FR24: An authenticated user can generate a PDF for an invoice from current invoice data.
FR25: An authenticated user can download the generated PDF file.
FR26: A generated PDF can include the seller, the client, the invoice date, line items, and totals required by the minimum implemented set for V1.
FR27: A generated PDF reflects the same invoice data as shown in the application for amounts, client identity, and line content.
FR28: The product can operate monetary amounts in EUR for V1.
FR29: When required invoice or client fields are missing or invalid, the user can see clear validation feedback and correct the input.
FR30: The product's behavior and labels avoid claiming legal certification beyond what is implemented; limitations can be communicated via product copy or documentation as agreed by the team.
FR31: A reviewer can identify the product version or release identifier exposed in the deployed application or documentation (as defined by the team) to match grading and support expectations.
FR32: An authenticated user can send an invoice by email to one or more recipient addresses, with a text message (subject/body as defined by the team) and the generated PDF for that invoice attached.
FR33: Email send uses the same invoice data as FR24–FR27 at send time; on failure, the user receives a clear error and the system does not claim success.
FR34: An authenticated user can export their clients to a CSV file with a documented column set.
FR35: An authenticated user can export their invoices to a CSV file with a documented column set (including amounts and identifiers consistent with stored data).
FR36: An authenticated user can view a dashboard showing total revenue across their invoices per documented revenue rules (e.g. based on status such as paid—team-defined and consistent with exports).
FR37: The dashboard shows the total count of invoices for the account.
FR38: The dashboard shows revenue aggregated by client.
FR39: The dashboard shows revenue aggregated by month (calendar month or billing period—team-defined).
FR40: All dashboard figures in FR36–FR39 are computed from persisted invoice (and related) data, not static placeholders.
FR41: An authenticated user can upload or replace a logo image used for invoice PDFs (format/size limits as defined by the team).
FR42: Generated PDFs can render the user's current logo without obscuring mandatory invoice content required by the implemented rules.
FR43: The product can apply a documented caching strategy to reduce redundant computation or database reads for dashboard and list endpoints while preserving correctness after create/update/delete operations affecting those views (invalidation or TTL documented per endpoint class).
```

### NonFunctional Requirements

```
NFR-P1: Primary interactive flows (sign-in, list/detail views, save invoice, trigger PDF) complete within a team-defined response-time budget acceptable for a web SaaS (exact thresholds recorded with the load-test report).
NFR-P2: A load test runs against the staging environment before promotion to production; metrics (e.g. throughput, latency percentiles, error rate) are documented and meet team-agreed thresholds for the V1 release.
NFR-P3: PDF generation may be slower than simple CRUD; the user receives feedback (e.g. in-progress state) so the UI does not appear frozen during generation.
NFR-S1: All browser-to-server traffic uses HTTPS in staging and production.
NFR-S2: Credentials and session tokens are handled using industry-standard practices (e.g. salted hashing for passwords, secure session or token settings); secrets are not committed to the repository.
NFR-S3: Authorization is enforced server-side for every mutating and read operation on domain data (clients, services, invoices, PDFs)—FR4 is technically guaranteed, not UI-only.
NFR-S4: Personal data (freelancer and client PII) is processed in line with GDPR expectations: minimal collection, restricted access, and a documented stance on retention and deletion in README or privacy notice as appropriate for V1.
NFR-SC1: V1 targets a small concurrent user base (course and early adopters); the architecture may assume modest load, but staging load tests must still demonstrate predictable behavior under the agreed scenario.
NFR-SC2: If traffic grows, the team can scale the deployment path (e.g. more instances, managed DB) without redesigning core domain rules—exact mechanism is implementation-specific.
NFR-A1: The web UI follows a reasonable baseline for keyboard and screen-reader use on primary flows (navigation, forms, actions); the team may target WCAG 2.1 Level A as a stretch goal where feasible for V1.
NFR-I1: No mandatory integration with external billing, tax, or accounting systems in V1; outbound PDF remains the primary artifact. Future integrations are out of scope for NFR validation unless added to the PRD.
NFR-I2 (V2): Transactional email integration (or equivalent) is required for FR32–FR33; provider credentials are environment-managed; rate limits and failures are handled without exposing secrets to clients. Transient failures: at least one bounded automatic retry before surfacing a user-visible failure (total wait and retry count documented in runbook); permanent failures return a clear, non-success outcome without implying delivery.
NFR-C1: Cached responses for dashboard and lists reflect eventual consistency bounded by a team-documented maximum staleness after writes, or use invalidation that guarantees read-your-writes for the acting user where feasible.
NFR-C2: Cache mechanism (e.g. process memory, shared external cache, or HTTP caching semantics) is implementation-specific but must be safe for multi-instance deployment if the team runs more than one app instance in staging/production.
NFR-O1: Application logs for API and background tasks use machine-parseable fields (severity level, timestamp, correlation identifier per request where applicable) consistent across services, and contain no unredacted secrets.
NFR-O2: A monitoring dashboard or page is reachable in staging and production via embedded health UI, vendor-hosted operations console, or provider-native metrics linked from README—without mandating a specific vendor—and exposes at minimum service liveness, recent errors or error rate, and optional additional metrics agreed by the team.
NFR-O3: CI/CD pipelines and deployment practices updated for V2 remain green on the default branch for the same classes of checks as V1 unless the team documents intentional scope changes.
NFR-G1: V2 ships with clean backlog hygiene (issues/PRs traceable to requirements FR32+), a tagged release (recommended v2.0.0 or team scheme), and release notes describing operational changes (email provider, cache, monitoring).
NFR-G2: The production and staging environments remain reachable after V2 rollout; rollback or feature-flag strategy is documented if email or cache introduces new failure modes.
```

### Additional Requirements

```
- **Epic 1 / Story 1 relevance — starters:** Initialize two separate repositories using official starters: Next.js via `npx create-next-app@latest` (TypeScript, ESLint, App Router, `--src-dir`, import alias `@/*`); NestJS via `nest new` with `@nestjs/cli@latest` (strict, npm). Pin resolved framework versions in package.json after scaffold.
- **Repository model:** Two Git repositories (frontend Next.js, backend NestJS); explicit API contract, CORS, aligned environment variables (API URL, secrets).
- **API:** REST under prefix `/api/v1`; OpenAPI/Swagger generated from Nest DTOs for evaluator documentation.
- **CORS:** Allow frontend origin(s) only; credentials policy aligned with JWT/cookie strategy.
- **Data:** PostgreSQL; ORM **Prisma or TypeORM** — team picks one and documents; migrations required.
- **Money and tax:** Store numeric/decimal types in DB; domain rules for minor units or fixed precision; **never float** for persisted monetary amounts.
- **Tenant isolation:** Column `userId` (or `accountId`) on all tenant-owned rows; every query filters by authenticated user; tests must cover forbidden cross-tenant access.
- **Auth:** JWT access (short-lived) + refresh (httpOnly cookie or documented secure storage); passwords hashed with bcrypt or Argon2.
- **JSON/API conventions:** camelCase in JSON; dates ISO-8601 UTC in API; unified error body: `statusCode`, `message`, optional `code`, `details` for validation.
- **PDF:** Generated only in NestJS from persisted invoice graph by id **and** userId; same services as persistence (no client-only truth for amounts).
- **Next.js patterns:** App Router under `src/app`; server components for read-heavy pages where appropriate; single typed API client module; auth via middleware or layout checks.
- **Infrastructure:** Dockerfile in each repo; docker-compose (or documented equivalent) for local full stack; separate CI pipelines: install → lint → format check → test → build → Docker build → publish → deploy to staging/production.
- **Git flow:** feature/* → develop → staging → main; load tests with recorded metrics against staging before production promotion.
- **Redis:** Use for cache, rate limits (per userId and route on login, PDF, future send-email), and BullMQ (or equivalent) job queues for async email and optional async PDF; `REDIS_URL` in backend `.env.example` when enabled.
- **Invoice caching (FR43 / NFR-C1):** Do not rely on long-lived cache for draft invoice financial snapshots; allow cache-aside for GET invoice by id and list/dashboard slices for statuses sent/paid/cancelled after Postgres commit; invalidate or update keys on any mutation affecting aggregates; do not store full PDF binaries in Redis; key naming includes tenant id (e.g. `user:{userId}:invoice:{invoiceId}`).
- **Observability (V2-aligned):** Grafana as visualization layer; Prometheus (metrics), Loki (logs), Tempo (traces) — LGTM pattern; prefer Grafana Cloud or self-hosted; OpenTelemetry in Nest first; structured JSON logs (level, requestId, route, statusCode) for Loki; no secrets, tokens, or full invoice payloads in logs; minimize raw email.
- **Email (V2):** Dev: SMTP_* in .env.example; production: transactional email API preferred; Nest: `@nestjs-modules/mailer`/Nodemailer or HTTP client to provider — one approach per environment, documented; attachments from persisted invoice (stream or short-lived temp file); async send via queue with retries.
- **Product/architecture gaps to close in stories:** Finalize **invoice status transition matrix** before implementing correction flows; decide **SIRET/SIREN/RCS** in or out of V1 schema and PDF; document Next ↔ Nest pattern (direct CORS vs Next Route Handlers as BFF) in README at kickoff.
- **Idempotency:** Document for PDF generation and future payment-related endpoints.
- **Success response shape:** Choose direct DTO vs `{ data: T }` and pagination wrapper `{ data, meta }` — team consistent.
- **Enforcement (Nest):** MUST enforce tenant filter on every persistence query for tenant data; MUST use same error JSON shape; MUST invalidate/update Redis per invoice mutations before returning success (or documented read-your-writes); MUST NOT cache draft financial snapshots with long TTL.
```

### UX Design Requirements

```
UX-DR1: Install and wrap Radix UI Primitives in `src/components/ui/` with Tailwind classes reading semantic CSS variables; feature code must not use one-off hex values—only semantic tokens (e.g. bg-primary, text-muted-foreground, border-border).
UX-DR2: Implement multiple named color themes (minimum: default, ocean, forest, high-contrast) as sets of CSS custom properties switched via `data-theme` on the document root (or root layout wrapper), persisted in localStorage; optional Radix Colors scales as source for harmonious palettes.
UX-DR3: Lock **Design Direction 1** for the authenticated shell: persistent **left sidebar** primary navigation (Factures, Clients, Prestations, Profil vendeur); active nav state per route; neutral chrome so themes swap without layout fork.
UX-DR4: Invoice create/edit layout: on viewports **lg and above** (≥1024px), **two-column** layout—main column for line editor and table, **sticky right rail** for HT / TVA / TTC summary; below **lg**, stack to **single column** (summary below lines or collapsible panel—team choice documented).
UX-DR5: Implement **InvoiceLineTable** (or `InvoiceLinesEditor`): add, remove, reorder lines; pick **service** to prefill description and unit HT; per-line HT, VAT rate, line totals with **tabular-nums** and EUR formatting; states: loading skeleton, editable (draft), read-only when status disallows edit, empty (no lines), row-level error; accessibility: table semantics, row headers, keyboard navigation through inputs.
UX-DR6: Implement **InvoiceTotalsPanel**: displays HT, VAT, TTC, currency EUR, and **status** badge; sticky on desktop; after save, values mirror server totals; loading on refetch; accessibility: region landmark or heading "Synthèse", values as text with tabular nums.
UX-DR7: Implement **InvoiceStatusActions**: expose allowed status transitions as primary/secondary buttons; disabled controls include **tooltip or helper text** explaining why; destructive actions open **Radix Dialog** confirmation with clear French copy; loading on mutation and error with retry.
UX-DR8: Implement **ResourceEmptyState** for Clients, Services, and Invoices lists: one headline, one short sentence, one primary CTA; variant for first-time vs optional "no results after filter" when filters exist.
UX-DR9: Implement **ThemeSwitcher** in header or settings: switches `data-theme` presets; optional light/dark (`data-appearance` or layered with theme); switching theme must not reset form state; respect prefers-color-scheme only as default until user sets explicit preference.
UX-DR10: Implement **MoneyDisplay** (or equivalent pattern): consistent EUR formatting and `font-variant-numeric: tabular-nums` on all monetary columns, totals, summaries, and PDF-related CTAs.
UX-DR11: Map invoice status visuals to **semantic** token roles (e.g. draft = muted, sent = primary/accent, paid = success, cancelled = muted or warning); **never rely on color alone**—pair **badge + French text label** (e.g. "Payée").
UX-DR12: Typography: modular scale for page title, section title, card title, body, caption; body minimum ~16px equivalent on forms; one primary professional sans-serif stack for UI and invoice tables (specific font chosen at implementation).
UX-DR13: Spacing: base unit 4px (0.25rem); rhythm steps 4, 8, 12, 16, 24, 32, 48; comfortable form padding; slightly tighter row height on invoice line tables on desktop for density.
UX-DR14: Focus and a11y baseline: visible **focus rings** using design token `--ring` on all interactive elements; no `outline: none` without replacement; form labels always visible (not placeholder-only); `aria-invalid` and `aria-describedby` on validation errors; honor **prefers-reduced-motion** for theme and drawer transitions.
UX-DR15: Responsive behavior: **tablet (768–1023px)** — sidebar may collapse to icon rail or drawer; invoice summary stacks below lines or collapsible top panel; **mobile (≤767px)** — single column, hamburger or bottom nav (pick one consistently); touch targets minimum **44×44px** for mobile nav and primary actions.
UX-DR16: **Feedback patterns:** toast for save, status change, PDF ready (avoid duplicate toasts); API errors mapped to **short actionable French messages** using Nest error `code` when available; field-level validation with non-invalid values preserved; PDF button shows explicit in-progress state (e.g. "Génération en cours…"); success feedback professional, not gimmicky.
UX-DR17: **Button hierarchy:** one **primary** per view for main forward action (Enregistrer, Générer le PDF, Créer une facture); secondary for safe alternatives; destructive (Annuler la facture, Supprimer) always with Dialog + destructive token.
UX-DR18: **French UI:** all user-visible strings in French; dates and numbers displayed with French locale via `Intl` while API remains UTC ISO strings per architecture.
UX-DR19: **Honest compliance framing:** optional fields (e.g. SIRET, VAT number) labeled optional when product defers them; copy must not imply legal certification beyond implemented rules (inline helpers or links to README as agreed).
UX-DR20: **Feature components must not import raw `@radix-ui/*` directly**—only wrapped components from `src/components/ui/` to enforce tokens and interaction consistency.
UX-DR21: **Journey A empty states:** Clients and Services lists must CTA toward creation before blocking New Invoice; optional check for **seller profile completeness** before PDF with inline prompt to complete profile.
UX-DR22: **Journey B (Mehdi):** invoice detail shows only legal actions per status; illegal transitions hidden or disabled with explanation; after correction path, user can regenerate PDF and verify parity with screen.
```

### FR Coverage Map

| FR | Epic | Notes |
|----|------|--------|
| FR1 | 1 | Registration |
| FR2 | 1 | Login |
| FR3 | 1 | Logout |
| FR4 | 1 (+ all) | Tenant isolation enforced on every domain story |
| FR5–FR6 | 1 | Seller profile; PDF consumption in Epic 5 |
| FR7–FR10 | 2 | Clients CRUD |
| FR11–FR14 | 3 | Services CRUD |
| FR15–FR23, FR28–FR30 | 4 | Invoices lifecycle, amounts, validation, copy |
| FR24–FR27 | 5 | PDF generation and parity |
| FR31 | 1 | Release / version identifier |
| FR34–FR40 | 6 | CSV exports + dashboard V2 |
| FR32–FR33, FR41–FR42 | 7 | Email + logo V2 |
| FR43 | 8 | Cache + invalidation V2 |

**UX-DR coverage:** DR1–DR3, DR8–DR10, DR12–DR20 (shell, themes, a11y baseline) → Epic 1 Story 1.7 and cross-cutting UI stories. DR4–DR7, DR10–DR11, DR21–DR22 → Epics 4–5. DR8 ResourceEmptyState → Epics 2–4 lists.

**NFR coverage:** Security (S1–S4), HTTPS, authz → Epics 1–5 and 7. Performance P1/P3 → Epics 4–5, 8. P2/SC1 load test → documented in Epic 8 / pipeline stories. A1 → UI stories Epics 1–5. I2, C*, O*, G* → Epics 7–8.

## Epic List

### Epic 1: Account, Session & Seller Identity
Enable signup, login, logout, tenant-safe access, seller profile for invoices/PDFs, and a visible release identifier for reviewers.
**FRs covered:** FR1, FR2, FR3, FR4, FR5, FR6, FR31

### Epic 2: Client Directory
Manage the freelancer’s clients (create, list, update, delete) with PRD fields.
**FRs covered:** FR7, FR8, FR9, FR10

### Epic 3: Service Catalog (Prestations)
Create and maintain reusable services (title, hourly rate) for invoice lines.
**FRs covered:** FR11, FR12, FR13, FR14

### Epic 4: Invoicing — Lines, Totals & Statuses
Create invoices, edit lines when allowed, compute and persist HT/VAT/TTC, manage statuses with explicit rules, list and detail views, EUR and honest compliance UX.
**FRs covered:** FR15–FR23, FR28, FR29, FR30

### Epic 5: Invoice PDF Aligned to Data
Generate and download PDF invoices from server-side persisted data with loading feedback and screen–PDF parity.
**FRs covered:** FR24, FR25, FR26, FR27 (uses FR5–FR6 from Epic 1)

### Epic 6: Insights & Data Exports (V2)
Deliver dashboard KPIs from stored invoices and UTF-8 CSV exports for clients and invoices with a documented column contract.
**FRs covered:** FR34, FR35, FR36, FR37, FR38, FR39, FR40

### Epic 7: Client Delivery & PDF Branding (V2)
Send invoices by email with PDF attachment and custom message; upload logo and render it on PDFs without obscuring mandatory fields.
**FRs covered:** FR32, FR33, FR41, FR42

### Epic 8: Cache, Freshness & Observability (V2)
Apply documented Redis caching for lists/dashboard with invalidation/read-your-writes; structured logs; reachable monitoring; multi-instance-safe cache; V2 release and ops hygiene.
**FRs covered:** FR43 — **NFRs:** NFR-C1, NFR-C2, NFR-O1, NFR-O2, NFR-O3, NFR-G1, NFR-G2

---

## Epic 1: Account, Session & Seller Identity

Visitors and freelancers can register, authenticate, sign out, maintain seller identity for billing documents, see deployment version, and land in a navigable app shell with theming and French UI baseline.

### Story 1.1: Visitor registration with email and password

As a visitor,
I want to create an account with email and password,
So that I can access FreelanceFlow with my own isolated workspace.

**Acceptance Criteria:**

**Given** a valid email and password meeting the team policy,
**When** I submit the registration form,
**Then** a user record is created with a salted password hash and I can sign in,
**And** validation errors are shown in French with field-level messages without clearing unrelated fields (FR1, FR29, NFR-S2, UX-DR16–DR18).

**Given** an email already registered,
**When** I submit registration,
**Then** the API returns a structured error and the UI shows a clear French message without leaking which field failed beyond “already used” policy (NFR-S2).

### Story 1.2: Registered user sign-in

As a registered user,
I want to sign in with my credentials,
So that I obtain an access token (and refresh strategy per architecture) for API calls.

**Acceptance Criteria:**

**Given** correct credentials,
**When** I submit login,
**Then** I receive a short-lived access token and the documented refresh mechanism is applied (httpOnly cookie or documented storage) (FR2, NFR-S2).

**Given** wrong credentials,
**When** I submit login,
**Then** I see a generic or policy-approved French error and no stack trace or secret is exposed (NFR-S2, S1 in non-local).

### Story 1.3: Authenticated sign-out

As an authenticated user,
I want to end my session from the product,
So that my tokens are invalidated or cleared per team policy on shared devices (FR3).

**Acceptance Criteria:**

**Given** I am signed in,
**When** I choose “Déconnexion”,
**Then** refresh/session state is cleared client-side and server-side invalidation is applied if the team implements a blocklist/rotation (FR3, NFR-S2).

### Story 1.4: Tenant isolation on protected APIs

As a product owner,
I want every domain read/write scoped by authenticated user,
**So that** FR4 is enforced server-side, not only in the UI (FR4, NFR-S3).

**Acceptance Criteria:**

**Given** two users A and B with separate data,
**When** user A calls any protected endpoint with user B’s resource id,
**Then** the API returns 404 or 403 and never returns B’s payload (FR4, NFR-S3).

**Given** automated tests,
**When** CI runs,
**Then** at least one test proves forbidden cross-tenant access for a representative resource introduced so far (users/profile) (NFR-S3).

### Story 1.5: Seller profile for invoice identity

As an authenticated user,
I want to maintain my seller details used on invoices and PDFs,
**So that** issued documents show correct freelancer identity within the V1 field set (FR5, FR6).

**Acceptance Criteria:**

**Given** I am authenticated,
**When** I open “Profil vendeur” and save valid data,
**Then** values persist and are returned on subsequent GET; optional fields (e.g. SIRET/TVA) are labeled optional if deferred (FR5, FR6, FR29, UX-DR19).

**Given** missing required seller fields per product rules,
**When** I save,
**Then** I see French validation messages tied to fields (FR29, UX-DR14).

### Story 1.6: Release or version identifier for reviewers

As a reviewer,
I want to see which release is deployed,
**So that** I can map production behaviour to a tag or build (FR31).

**Acceptance Criteria:**

**Given** deployed staging/production,
**When** I read README or hit the documented version endpoint or UI footer,
**Then** I see a non-empty version/build string aligned with the team’s tagging scheme (FR31).

### Story 1.7: Authenticated app shell, themes, and navigation chrome

As an authenticated user,
I want a persistent sidebar, theme presets, and accessible chrome,
**So that** navigation matches Design Direction 1 and supports future feature routes (UX-DR1–DR3, DR8–DR10, DR12–DR20, NFR-A1).

**Acceptance Criteria:**

**Given** I am authenticated,
**When** I load any in-app route,
**Then** I see a left sidebar with entries for Factures, Clients, Prestations, Profil vendeur with active state; layout uses semantic tokens only in feature areas (UX-DR1–DR3, DR20).

**Given** I use the theme switcher,
**When** I select default, ocean, forest, or high-contrast,
**Then** `data-theme` updates, choice persists in `localStorage`, and focus rings remain visible (UX-DR2, DR9, DR14).

**Given** viewport below `lg`,
**When** I resize the window,
**Then** navigation follows the documented responsive pattern (drawer/hamburger/icon rail) with ≥44px touch targets for primary nav controls (UX-DR15).

---

## Epic 2: Client Directory

Freelancers can maintain their client records used for invoicing.

### Story 2.1: Create client with required fields

As an authenticated user,
I want to create a client with name, email, company, and address,
**So that** I can bill that client later (FR7, FR29, UX-DR8, DR10, DR18).

**Acceptance Criteria:**

**Given** valid field values,
**When** I submit the create form,
**Then** the client is stored with my `userId` and appears in the list (FR7, FR4).

**Given** invalid or missing required values,
**When** I submit,
**Then** I see French field errors and the API returns structured validation details (FR29, NFR-S3).

### Story 2.2: List my clients

As an authenticated user,
I want to see a list of my clients,
**So that** I can open or edit them quickly (FR8, UX-DR8, DR10).

**Acceptance Criteria:**

**Given** I have zero clients,
**When** I open Clients,
**Then** I see `ResourceEmptyState` with one primary CTA to create a client (FR8, UX-DR8, DR21).

**Given** I have clients,
**When** I open Clients,
**Then** I see a table or list with aligned text and tabular numbers where amounts are not yet applicable—company/name readable (FR8, UX-DR12–DR13).

### Story 2.3: Update an existing client

As an authenticated user,
I want to edit a client’s information,
**So that** billing details stay current (FR9, FR29).

**Acceptance Criteria:**

**Given** a client I own,
**When** I save edits,
**Then** persisted fields update and list/detail reflect changes (FR9, FR4).

### Story 2.4: Delete a client

As an authenticated user,
I want to delete a client I no longer need,
**So that** my directory stays accurate (FR10, UX-DR17).

**Acceptance Criteria:**

**Given** a client I own with no blocking business rules (or documented constraint if invoices reference them),
**When** I confirm deletion in a destructive dialog,
**Then** the client is removed for my account only and others’ data is unaffected (FR10, FR4, UX-DR7, DR17).

---

## Epic 3: Service Catalog (Prestations)

Freelancers define reusable services to speed invoice line entry.

### Story 3.1: Create service with title and hourly rate

As an authenticated user,
I want to create a service with title and hourly HT rate,
**So that** I can reuse it on invoices (FR11, FR28, FR29, UX-DR8, DR10).

**Acceptance Criteria:**

**Given** valid title and non-float monetary rate storage,
**When** I save,
**Then** the service is persisted scoped to my user (FR11, FR4, architecture money rules).

### Story 3.2: List my services

As an authenticated user,
I want to list my services,
**So that** I can manage my catalog (FR12, UX-DR8).

**Acceptance Criteria:**

**Given** no services,
**When** I open Prestations,
**Then** empty state CTA guides creation before invoicing (FR12, UX-DR8, DR21).

### Story 3.3: Update a service

As an authenticated user,
I want to edit a service,
**So that** rates and titles stay accurate (FR13).

**Acceptance Criteria:**

**Given** a service I own,
**When** I save changes,
**Then** updates persist and existing invoices are not silently rewritten (snapshot semantics documented in UI helper if lines copy service data) (FR13, UX-DR optional helper).

### Story 3.4: Delete a service

As an authenticated user,
I want to delete a service,
**So that** obsolete offerings are removed (FR14, UX-DR17).

**Acceptance Criteria:**

**Given** a service I own and deletion policy (blocked if referenced by invoices, or soft-delete—team documented),
**When** I confirm delete,
**Then** behaviour matches the rule without cross-tenant side effects (FR14, FR4, UX-DR17).

---

## Epic 4: Invoicing — Lines, Totals & Statuses

Freelancers create invoices, manage lines and French VAT totals, transition statuses with clear rules, and navigate list/detail with trustworthy figures.

### Story 4.1: Create draft invoice linked to one client

As an authenticated user,
I want to create a new invoice draft for a selected client,
**So that** I can add lines next (FR15, FR4, UX-DR21).

**Acceptance Criteria:**

**Given** at least one client,
**When** I create an invoice,
**Then** a draft exists with `userId`, `clientId`, initial status `draft`, and I can open its detail page (FR15).

**Given** no clients,
**When** I attempt “Nouvelle facture”,
**Then** I am guided to create a client first (UX-DR21).

### Story 4.2: Invoice line editor with service pick and EUR display

As an authenticated user,
I want to add, remove, reorder, and edit lines with optional service pick,
**So that** amounts reflect my sold work (FR16, FR17, FR28, UX-DR4–DR5, DR10, DR13).

**Acceptance Criteria:**

**Given** a draft invoice,
**When** I add a line from a service,
**Then** description and unit HT prefill and I can adjust quantity/hours per rules (FR16, UX-DR5).

**Given** a non-draft status that forbids edits,
**When** I view the editor,
**Then** controls are read-only with an explanation (FR17, FR20 rules, UX-DR5, DR22).

### Story 4.3: Server-side HT, VAT, and TTC computation and persistence

As an authenticated user,
I want totals computed and stored on the server,
**So that** UI and future PDF use one source of truth (FR18, FR23, FR28, NFR-S3).

**Acceptance Criteria:**

**Given** saved line inputs,
**When** the server recalculates,
**Then** HT, VAT, and TTC match team-defined French VAT rules and are persisted using non-float types (FR18, FR23, architecture).

**Given** concurrent edits,
**When** two saves race,
**Then** behaviour is last-write-wins or version conflict per team choice, documented (FR23).

### Story 4.4: Invoice list, detail, sticky totals panel, and responsive layout

As an authenticated user,
I want list and detail views with a visible HT/VAT/TTC summary,
**So that** I can trust figures before PDF (FR19, FR21, FR22, UX-DR4, DR6, DR10–DR11).

**Acceptance Criteria:**

**Given** saved invoice data,
**When** I open detail on desktop (`lg+`),
**Then** I see two columns: lines + sticky “Synthèse” with HT/VAT/TTC, EUR, and status badge with text label (FR19, FR22, UX-DR4, DR6, DR11).

**Given** a narrow viewport,
**When** I open the same invoice,
**Then** summary stacks under lines per documented responsive behaviour (UX-DR4, DR15).

### Story 4.5: Invoice status transitions with explicit matrix

As an authenticated user,
I want to transition status among draft, sent, paid, cancelled per published rules,
**So that** corrections follow Mehdi’s journey safely (FR20, UX-DR7, DR11, DR22).

**Acceptance Criteria:**

**Given** the documented transition matrix (e.g. draft→sent, sent→paid, allowed corrections),
**When** I trigger an allowed transition,
**Then** status updates and illegal actions are disabled or hidden with helper text (FR20, UX-DR7, DR22).

**Given** a destructive transition (e.g. cancel),
**When** I confirm in a dialog,
**Then** status updates accordingly with French copy (UX-DR7, DR17).

### Story 4.6: Validation feedback and honest compliance copy on invoice flows

As an authenticated user,
I want clear validation and scope-honest messaging,
**So that** I do not over-trust legal coverage (FR29, FR30, UX-DR16, DR19).

**Acceptance Criteria:**

**Given** server validation errors on save,
**When** the API returns `details`,
**Then** the UI maps codes to short French messages and preserves valid field values (FR29, UX-DR16).

**Given** invoice or seller areas referencing legal fields,
**When** I read helper text,
**Then** it states implemented vs optional identifiers per product decision (FR30, UX-DR19).

---

## Epic 5: Invoice PDF Aligned to Data

Users download PDF invoices generated in Nest from persisted rows, with visible progress and parity to the UI.

### Story 5.1: Backend PDF generation from persisted invoice

As an authenticated user,
I want the server to build a PDF from my invoice id,
**So that** amounts cannot diverge from the database (FR24, FR26, architecture PDF module).

**Acceptance Criteria:**

**Given** an invoice I own,
**When** I call the PDF endpoint,
**Then** Nest loads invoice graph filtered by `userId` and streams PDF bytes; other users’ ids return 404/403 (FR24, FR4, architecture).

**Given** Puppeteer/PDFKit choice,
**When** generation runs,
**Then** implementation follows architecture (HTML template or programmatic) without storing PDF in Redis (architecture).

### Story 5.2: Download PDF with in-progress UI

As an authenticated user,
I want to download the PDF with clear progress,
**So that** I know generation is working (FR25, NFR-P3, UX-DR16).

**Acceptance Criteria:**

**Given** I trigger “Générer le PDF”,
**When** generation takes noticeable time,
**Then** the button shows an explicit in-progress state and disables double-submit until completion or error (FR25, NFR-P3, UX-DR16).

**Given** success,
**When** the file is ready,
**Then** the browser downloads or opens the file per UX choice (FR25).

### Story 5.3: PDF content parity and seller inclusion

As an authenticated user,
I want the PDF to match the on-screen invoice and include seller and client blocks,
**So that** I can send a credible document (FR26, FR27, FR6, UX-DR21).

**Acceptance Criteria:**

**Given** identical invoice data,
**When** I compare PDF to detail view,
**Then** client identity, line text, quantities, HT/VAT/TTC match persisted values (FR27).

**Given** incomplete seller profile when required for PDF,
**When** I attempt PDF,
**Then** I see a blocking message with link to Profil vendeur (FR6, UX-DR21).

**Given** automated tests,
**When** CI runs,
**Then** at least one parity check exists (hash or golden snapshot) for a fixture invoice (FR27).

---

## Epic 6: Insights & Data Exports (V2)

Freelancers export CSV data and view dashboard KPIs computed from stored invoices per measurement appendix defaults unless overridden.

### Story 6.1: Export clients to UTF-8 CSV

As an authenticated user,
I want to download my clients as CSV,
**So that** I can use them in spreadsheets (FR34, NFR-S3).

**Acceptance Criteria:**

**Given** my clients,
**When** I request export,
**Then** I receive UTF-8 CSV with documented columns and `export-schema` version in README or API doc (FR34, PRD appendix).

### Story 6.2: Export invoices to UTF-8 CSV

As an authenticated user,
I want to download my invoices as CSV,
**So that** my accountant can reconcile (FR35, FR4).

**Acceptance Criteria:**

**Given** my invoices,
**When** I export,
**Then** amounts and identifiers match stored data and column set is versioned (FR35).

### Story 6.3: Dashboard totals — revenue (paid TTC rules) and invoice count

As an authenticated user,
I want to see total revenue and invoice count from my data,
**So that** I understand business volume (FR36, FR37, FR40, PRD measurement defaults).

**Acceptance Criteria:**

**Given** invoices with mixed statuses,
**When** I open the dashboard,
**Then** “revenue” uses only **paid** invoices with TTC in EUR per documented attribution date field; count includes all invoices or documented rule (FR36–FR37, FR40, PRD appendix).

### Story 6.4: Dashboard breakdowns by client and by month

As an authenticated user,
I want revenue by client and by month,
**So that** I see concentration and seasonality (FR38, FR39, FR40).

**Acceptance Criteria:**

**Given** paid invoices across months and clients,
**When** I view the dashboard,
**Then** aggregations use the same rules as Story 6.3 and calendar month in Europe/Paris unless README states UTC (FR38–FR40, PRD appendix).

---

## Epic 7: Client Delivery & PDF Branding (V2)

Freelancers brand PDFs with a logo and send invoices by email with attachments and resilient delivery semantics.

### Story 7.1: Upload or replace invoice logo

As an authenticated user,
I want to upload a bounded logo image,
**So that** my PDFs carry my studio identity (FR41, NFR-S3).

**Acceptance Criteria:**

**Given** an image within format/size limits,
**When** I upload,
**Then** it stores securely scoped to my user and previews in settings (FR41, FR4).

**Given** an oversized or invalid file,
**When** I upload,
**Then** I see a French validation error (FR29).

### Story 7.2: Render logo on generated PDFs

As an authenticated user,
I want my current logo on PDFs,
**So that** branding is visible without hiding mandatory invoice content (FR42, FR26).

**Acceptance Criteria:**

**Given** a stored logo,
**When** I generate PDF,
**Then** the logo appears in the header area without obscuring mandatory fields (FR42).

### Story 7.3: Send invoice email with PDF attachment and message

As an authenticated user,
I want to email an invoice to one or more recipients with a message and PDF,
**So that** clients receive the same file as in-app generation (FR32, FR33, NFR-I2, UX-DR16).

**Acceptance Criteria:**

**Given** valid recipients and message,
**When** I send,
**Then** the PDF bytes are generated from persisted invoice at send time; success is reported only when provider accepts per integration semantics (FR32–FR33).

**Given** a transient provider error,
**When** the worker retries within documented bounds,
**Then** at most one automatic retry occurs before surfacing failure (NFR-I2).

**Given** a permanent failure (bad mailbox),
**When** send completes,
**Then** I see a clear non-success French message and no “sent” claim (FR33, NFR-I2).

### Story 7.4: Async email queue and rate limiting hooks

As a platform operator,
I want outbound email via queue and sensible rate limits,
**So that** API latency stays predictable and abuse is reduced (NFR-I2, architecture mail+queue).

**Acceptance Criteria:**

**Given** send requests,
**When** they arrive,
**Then** jobs enqueue to BullMQ/Redis and credentials stay in environment variables only (architecture, NFR-I2).

**Given** sensitive routes,
**When** traffic spikes,
**Then** per-`userId` rate limits apply to send and login endpoints per architecture (architecture).

---

## Epic 8: Cache, Freshness & Observability (V2)

Operators gain structured logs and a monitoring entry point; users benefit from cached lists/dashboard with correct invalidation across instances.

### Story 8.1: Structured JSON logging with correlation id

As an operator,
I want JSON logs with severity, timestamp, service, correlation id,
**So that** I can troubleshoot without PII/secrets leakage (NFR-O1, architecture).

**Acceptance Criteria:**

**Given** HTTP requests,
**When** they complete,
**Then** each log line includes the agreed machine-parseable fields and excludes secrets and full invoice payloads (NFR-O1, architecture PII rules).

### Story 8.2: Reachable monitoring / health surface

As an operator,
I want staging and production links to health/errors/metrics,
**So that** I can verify liveness quickly (NFR-O2).

**Acceptance Criteria:**

**Given** staging and production,
**When** I follow README links or embedded health UI,
**Then** I can see liveness and recent errors or error rate plus optional metrics placeholder (NFR-O2).

### Story 8.3: Redis cache-aside for invoice lists and dashboard with invalidation

As an authenticated user,
I want fast lists and dashboard after writes stay correct,
**So that** FR43 and NFR-C1/C2 are satisfied (FR43, NFR-C1, NFR-C2, architecture caching table).

**Acceptance Criteria:**

**Given** invoice mutations affecting aggregates,
**When** writes succeed,
**Then** affected cache keys for that user’s lists/dashboard/detail (per stable-band rules) are invalidated or updated before response completes (FR43, NFR-C1, architecture).

**Given** draft invoices,
**When** cached,
**Then** no long-lived financial snapshot cache is used (architecture).

**Given** multiple app instances,
**When** reads use Redis,
**Then** configuration is shared-cache safe (NFR-C2).

### Story 8.4: V2 CI/CD, release notes, and rollback/runbook updates

As a team member,
I want green pipelines and documented ops for V2 features,
**So that** staging/production stay reachable and traceable (NFR-O3, NFR-G1, NFR-G2).

**Acceptance Criteria:**

**Given** V2 changes (email, Redis, monitoring),
**When** CI runs on default branch,
**Then** lint/format/test/build/Docker steps remain green or waivers are documented (NFR-O3).

**Given** release time,
**When** we ship V2,
**Then** issues/PRs trace to FR32+ and a tagged release with release notes exists; rollback/feature-flag notes cover email/cache failures (NFR-G1, NFR-G2).
