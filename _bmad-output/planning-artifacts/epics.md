---

## stepsCompleted:

- step-01-validate-prerequisites
- step-02-design-epics
- step-03-create-stories
- step-04-final-validation
workflowStatus: complete
workflowType: create-epics-and-stories
completedAt: '2026-03-27'
inputDocuments:
- _bmad-output/planning-artifacts/prd.md
- _bmad-output/planning-artifacts/architecture.md
- _bmad-output/planning-artifacts/ux-design-specification.md
workflowNote: "ux-design-directions.html explicitly excluded per stakeholder (2026-03-27)."

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
```

### NonFunctional Requirements

```
NFR1: Primary interactive flows (sign-in, list/detail views, save invoice, trigger PDF) complete within a team-defined response-time budget acceptable for a web SaaS (exact thresholds recorded with the load-test report).
NFR2: A load test runs against the staging environment before promotion to production; metrics (e.g. throughput, latency percentiles, error rate) are documented and meet team-agreed thresholds for the V1 release.
NFR3: PDF generation may be slower than simple CRUD; the user receives feedback (e.g. in-progress state) so the UI does not appear frozen during generation.
NFR4: All browser-to-server traffic uses HTTPS in staging and production.
NFR5: Credentials and session tokens are handled using industry-standard practices (e.g. salted hashing for passwords, secure session or token settings); secrets are not committed to the repository.
NFR6: Authorization is enforced server-side for every mutating and read operation on domain data (clients, services, invoices, PDFs)—FR4 is technically guaranteed, not UI-only.
NFR7: Personal data (freelancer and client PII) is processed in line with GDPR expectations: minimal collection, restricted access, and a documented stance on retention and deletion in README or privacy notice as appropriate for V1.
NFR8: V1 targets a small concurrent user base (course and early adopters); the architecture may assume modest load, but staging load tests must still demonstrate predictable behavior under the agreed scenario.
NFR9: If traffic grows, the team can scale the deployment path (e.g. more instances, managed DB) without redesigning core domain rules—exact mechanism is implementation-specific.
NFR10: The web UI follows a reasonable baseline for keyboard and screen-reader use on primary flows (navigation, forms, actions); the team may target WCAG 2.1 Level A as a stretch goal where feasible for V1.
NFR11: No mandatory integration with external billing, tax, or accounting systems in V1; outbound PDF remains the primary artifact. Future integrations are out of scope for NFR validation unless added to the PRD.
```

### Additional Requirements

```
- Epic 1 / first implementation story context: Greenfield bootstrap using official starters — Next.js via `create-next-app@latest` (TypeScript, ESLint, App Router, src dir) in a dedicated frontend repo; NestJS via `@nestjs/cli@latest` (`nest new`) in a dedicated backend repo; pin resolved versions in package.json after init.
- Two separate Git repositories (frontend Next.js, backend NestJS); aligned env vars (API URL, secrets); API contract is versioned REST under `/api/v1`.
- OpenAPI/Swagger generated from Nest DTOs; contract documentation required for evaluators.
- CORS: allow frontend origin(s) only; credentials policy aligned with JWT/cookie strategy.
- Database: PostgreSQL; ORM/migrations: Prisma (recommended) or TypeORM — team picks one consistently; tenant column `userId` (or `accountId`) on all tenant-owned rows; every query filters by authenticated user; never use binary floating point for persisted monetary amounts; store numeric/decimal or minor units per domain rules.
- Authentication: JWT access (short-lived) + refresh (httpOnly cookie or documented secure storage); passwords hashed with bcrypt or Argon2; Nest guards on every controller; validate resource ownership in services (e.g. 404 for other user's invoice).
- JSON: camelCase; dates in API as ISO-8601 UTC; display localization in Next for French users.
- Errors: unified JSON shape (statusCode, message, optional code, details for validation); Nest exception filter maps all HTTP errors to this shape.
- PDF: generated only in Nest from persisted invoice graph by id and userId (Puppeteer HTML→PDF or PDFKit/similar); Next triggers download endpoint; idempotency documented for PDF generation where relevant.
- Next.js: App Router under `src/app`; server components for read-heavy pages where appropriate; typed API client module in `src/lib/api/`; protected routes via middleware or layout + token checks; team documents whether browser calls Nest directly (CORS) or via Next Route Handlers (BFF).
- Structure: Nest feature modules under `src/modules/<feature>/`; Next `src/components/ui/` and `src/components/features/<domain>/`; backend tests as `*.spec.ts` colocated unless team standardizes otherwise.
- Infrastructure: Dockerfile in each repo; docker-compose (or equivalent) to run full stack locally; separate CI pipelines per repo: install → lint → format check → test → build → Docker build → registry publish → deploy to staging and production.
- Git flow: feature/* → develop → staging → main; load tests run against staging with recorded metrics before production promotion.
- Enforcement: MUST tenant-filter all persistence; MUST same error JSON shape; MUST PDF from backend persisted data only; MUST NOT float for money in DB.
- Product/architecture gaps to close in implementation planning: explicit in/out scope for SIREN/SIRET/RCS in V1; finalize invoice status transition matrix before coding correction flows (Mehdi journey).
```

### UX Design Requirements

```
UX-DR1: Implement the UI foundation with Radix UI Primitives wrapped in `src/components/ui/`, Tailwind CSS, and semantic design tokens as CSS custom properties (e.g. background, foreground, primary, destructive, ring); feature code must not use raw hex—only token-based utilities.
UX-DR2: Implement multiple named color themes (at minimum default, ocean, forest, high-contrast) switched via `data-theme` on the document root, persisted in localStorage; optional Radix Colors scales mapped into tokens; validate contrast for text, buttons, errors, and focus rings per theme including high-contrast.
UX-DR3: Optional light/dark: support `data-appearance` (or equivalent) layered with `data-theme`; respect prefers-color-scheme only as default until user sets an explicit preference; respect prefers-reduced-motion for non-essential animations (including theme transitions).
UX-DR4: Lock app shell to Design Direction 1: persistent left sidebar for primary nav (Factures, Clients, Prestations, Profil vendeur), active route highlighting, logo/account/sign-out in chrome; root layout implements this shell for all authenticated routes.
UX-DR5: Invoice create/edit/detail: on viewports lg and above, use two-column layout with line editor in main column and sticky right rail for HT / TVA / TTC summary and status; below lg, stack summary below lines or use collapsible panel; align breakpoints with Tailwind defaults (critical switch at lg 1024px).
UX-DR6: Implement InvoiceLineTable (or InvoiceLinesEditor): add/remove/reorder lines, pick service to prefill description and unit HT, per-line HT/VAT/TTC and EUR formatting, states for loading, editable vs read-only by status, empty and row error states; semantic table with tabular-nums; keyboard-friendly cells where applicable.
UX-DR7: Implement InvoiceTotalsPanel: displays HT, VAT, TTC, EUR, and status badge aligned with server after save; loading on refetch; landmark/heading for “Synthèse”; tabular numeric presentation.
UX-DR8: Implement InvoiceStatusActions: expose only allowed status transitions as primary/secondary buttons; disabled actions show tooltip or helper explaining why; destructive transitions open Radix Dialog with French confirmation copy; loading and error retry on mutation.
UX-DR9: Implement ResourceEmptyState for Clients, Services, and Invoices lists with one headline, one sentence, and one primary CTA toward the next step in the journey (per UX journeys A–C).
UX-DR10: Implement ThemeSwitcher in header or settings to change `data-theme` without resetting form state; persist selection in localStorage.
UX-DR11: Implement MoneyDisplay (or equivalent pattern) for consistent EUR formatting and tabular figures everywhere amounts appear (lists, summaries, PDF CTA area).
UX-DR12: All user-visible strings in French; map Nest structured errors (`code`, `details`) to short, actionable French messages (toast or inline alert); field-level validation with labels always visible, aria-invalid and aria-describedby on errors; preserve non-invalid field values on failure.
UX-DR13: PDF trigger: explicit loading state and copy (e.g. “Génération en cours…”); success path enables download; failure shows retry and error detail; consider aria-live for generation result for screen readers.
UX-DR14: Follow button hierarchy: one primary per view for main forward action; destructive actions use destructive token + confirmation dialog; disabled controls never silent—tooltip or helper when blocked by status or permissions.
UX-DR15: Feedback: toast for save, status change, PDF ready (avoid duplicates); validation summary optional; loading skeletons on lists; spinner/disabled on long actions.
UX-DR16: Navigation: list → detail with back to list; deep links to invoice URLs; breadcrumbs or back affordances where specified; invoice list supports at least minimal sort by date/status when scoped in V1 polish phase.
UX-DR17: Responsive: tablet sidebar may collapse to icon rail or drawer (team choice); mobile single column with hamburger or bottom nav (pick one); touch targets minimum 44×44px for primary nav and actions; horizontal scroll wrapper for wide tables on narrow screens if needed.
UX-DR18: Accessibility: WCAG 2.1 Level A baseline on primary flows; focus visible via ring token on all interactive elements; invoice status conveyed with badge + text not color alone; semantic landmarks (main, nav); decorative icons aria-hidden; icon-only buttons have French aria-label.
UX-DR19: Typography: professional neutral sans (team-chosen web or system stack); modular type scale; body ~16px minimum for forms; tabular-nums on all monetary columns.
UX-DR20: Copy and empty states align with V1 scope: honest framing for optional SIRET/TVA; no legal certification claims beyond implemented rules (supports FR30).
UX-DR21: Prerequisite prompts: when seller profile or first client is missing, guide user inline toward profile or client creation before blocking invoice/PDF flows (Journey A).
UX-DR22: Document snapshot semantics where lines copy catalog data at issue time: short inline help so users understand catalog changes do not rewrite past invoices (where product rules define snapshots).
UX-DR23: No raw `@radix-ui/*` imports in feature folders—only wrapped components from `src/components/ui/` to enforce tokens and patterns.
```

### FR Coverage Map

FR1: Epic 1 — Account registration  
FR2: Epic 1 — Sign-in  
FR3: Epic 1 — Sign-out  
FR4: Epic 1 (foundation); enforced in Epics 2–5 — Tenant isolation on every domain API  
FR5: Epic 1 — Seller profile maintenance  
FR6: Epic 1 & 5 — Seller on invoices/PDFs  
FR7: Epic 2 — Create client  
FR8: Epic 2 — List clients  
FR9: Epic 2 — Update client  
FR10: Epic 2 — Delete client  
FR11: Epic 3 — Create service  
FR12: Epic 3 — List services  
FR13: Epic 3 — Update service  
FR14: Epic 3 — Delete service  
FR15: Epic 4 — Create invoice for one client  
FR16: Epic 4 — Add invoice lines from services and quantities  
FR17: Epic 4 — Remove/adjust lines when status allows  
FR18: Epic 4 — Compute HT, VAT, TTC per V1 rules  
FR19: Epic 4 — View HT, VAT, TTC before PDF  
FR20: Epic 4 — Status transitions  
FR21: Epic 4 — List invoices  
FR22: Epic 4 — Invoice detail  
FR23: Epic 4 — Persisted totals consistent with lines  
FR24: Epic 5 — Generate PDF  
FR25: Epic 5 — Download PDF  
FR26: Epic 5 — PDF mandatory content set for V1  
FR27: Epic 5 — PDF parity with application data  
FR28: Epic 4 — EUR for monetary amounts  
FR29: Epics 1–4 — Validation and clear feedback on forms/API  
FR30: Epic 6 — Honest copy and documentation (no over-claiming)  
FR31: Epic 6 — Version/release identifier for reviewers  

## Epic List

### Epic 1: Account, Session, and Seller Profile

After this epic, the user can register, sign in, sign out, and maintain seller identity for invoicing; tenant isolation and auth guards are established for all later domains.

**FRs covered:** FR1, FR2, FR3, FR4 (foundation), FR5, FR6 (data available for later invoice/PDF epics)

**NFR / architecture touched:** NFR5, NFR6; starters; PostgreSQL + User; JWT; unified error shape; bcrypt/Argon2.

**UX-DR touched:** UX-DR1, UX-DR2, UX-DR3, UX-DR4 (shell), UX-DR10, UX-DR11 (where amounts appear later), UX-DR12, UX-DR14, UX-DR15, UX-DR17, UX-DR18, UX-DR19, UX-DR20.

### Epic 2: Client Directory

The user can create, list, update, and delete clients (name, email, company, address) scoped to their account.

**FRs covered:** FR7, FR8, FR9, FR10

**NFR / architecture:** NFR6; tenant `userId` on clients; decimal/money rules N/A for clients; validation DTOs.

**UX-DR touched:** UX-DR9, UX-DR11, UX-DR12, UX-DR14, UX-DR15, UX-DR16, UX-DR17, UX-DR18, UX-DR23.

### Epic 3: Billable Services (Prestations)

The user can define and manage services (title, hourly HT rate) for reuse on invoices.

**FRs covered:** FR11, FR12, FR13, FR14

**NFR / architecture:** NFR6; non-float storage for rates; tenant scoping.

**UX-DR touched:** UX-DR9, UX-DR11, UX-DR12, UX-DR14, UX-DR15, UX-DR16, UX-DR17, UX-DR18, UX-DR23.

### Epic 4: Invoices — Lines, Amounts, and Status

The user can build invoices with lines, see HT/VAT/TTC in EUR, persist consistent totals, and move through statuses per the product transition matrix.

**FRs covered:** FR15, FR16, FR17, FR18, FR19, FR20, FR21, FR22, FR23, FR28

**NFR / architecture:** NFR6; server-side totals; status matrix documented; snapshot semantics if product defines them.

**UX-DR touched:** UX-DR5, UX-DR6, UX-DR7, UX-DR8, UX-DR9, UX-DR11, UX-DR12, UX-DR14, UX-DR15, UX-DR16, UX-DR17, UX-DR18, UX-DR20, UX-DR21, UX-DR22.

### Epic 5: Invoice PDF and Document Parity

The user can generate and download a PDF that matches persisted invoice data (seller, client, lines, totals for V1).

**FRs covered:** FR24, FR25, FR26, FR27 (FR6 completion on PDF output)

**NFR / architecture:** PDF only in Nest from persisted graph + `userId`; NFR3 UI feedback.

**UX-DR touched:** UX-DR11, UX-DR13, UX-DR14, UX-DR15, UX-DR18.

### Epic 6: Quality, Deployment, and Traceability

The product is runnable via Docker, deployed through CI/CD with staging gates and load-test evidence, documented for evaluators, and labeled with an honest legal/scope posture.

**FRs covered:** FR30, FR31

**NFR / architecture:** NFR1 (budget + report), NFR2, NFR4, NFR7, NFR8, NFR9 (notes), NFR10 (verification), NFR11; OpenAPI published; Git flow; secrets hygiene.

**UX-DR touched:** UX-DR20 (copy in app/docs); any remaining a11y checklist UX-DR18 on key routes.

---

## Epic 1: Account, Session, and Seller Profile

Users can onboard, authenticate, end their session, and maintain seller fields required for V1 invoices; every subsequent module reuses tenant isolation established here.

### Story 1.1: Backend — NestJS bootstrap, PostgreSQL, and user registration

As a visitor,  
I want to register with email and password,  
So that I obtain an account to use FreelanceFlow.

**Acceptance Criteria:**

**Given** the NestJS API is running with PostgreSQL and a `User` model (or equivalent) scoped for future tenant fields  
**When** I submit valid registration data to `POST /api/v1/auth/register` (or team path)  
**Then** my password is stored using bcrypt or Argon2 and I receive a success response without leaking the hash  
**And** duplicate email is rejected with the unified JSON error shape (`statusCode`, `message`, optional `code`, `details`)  
**And** unit tests cover success and validation failures (FR1, NFR5, NFR6 for this endpoint).

### Story 1.2: Backend — Login, JWT access and refresh, and authenticated context

As a registered user,  
I want to sign in and receive tokens,  
So that I can call protected APIs securely.

**Acceptance Criteria:**

**Given** a registered user  
**When** they authenticate with correct credentials  
**Then** they receive a short-lived access JWT and a refresh strategy (httpOnly cookie or documented secure storage) per architecture  
**And** invalid credentials return 401 with the unified error body without revealing which field failed  
**And** an auth guard extracts `userId` (or `accountId`) for downstream handlers (FR2, NFR5, NFR6).

### Story 1.3: Backend — Logout and session end semantics

**As an authenticated user,**  
**I want to sign out,**  
**So that my session ends on this device as expected for V1.**

**Acceptance Criteria:**

**Given** an authenticated session  
**When** the user invokes logout (server endpoint and/or client clearing per team decision documented in README)  
**Then** refresh tokens are invalidated or rotated per chosen strategy, or client clears stored tokens safely  
**And** subsequent protected calls fail until login again (FR3).

### Story 1.4: Backend — Seller profile read and update

As an authenticated user,  
I want to read and update my seller information used on invoices,  
So that my documents show correct identity for the V1 field set.

**Acceptance Criteria:**

**Given** an authenticated user  
**When** they `GET` and `PATCH` seller profile fields defined for V1 (team documents mandatory vs optional identifiers such as SIRET/TVA)  
**Then** only their own profile is returned or updated (404/403 for others)  
**And** validation errors use the unified error JSON and field-level `details` where applicable (FR5, FR6 data ready, FR4, FR29).

### Story 1.5: Frontend — Next.js bootstrap, design system tokens, and auth screens

As a visitor or user,  
I want French-language registration and login screens with accessible forms,  
So that I can use the app without confusion or blocked input.

**Acceptance Criteria:**

**Given** a new Next.js app (App Router, `src/app`) with Tailwind and Radix primitives wrapped only in `src/components/ui/` (UX-DR23)  
**When** the user opens register and login pages  
**Then** copy is in French, labels are visible, focus rings use the `ring` token, and theme tokens (`default`, `ocean`, `forest`, `high-contrast`) switch via `data-theme` with `localStorage` persistence (UX-DR1, UX-DR2, UX-DR10, UX-DR12, UX-DR14, UX-DR18, UX-DR19, UX-DR20)  
**And** optional `data-appearance` and `prefers-reduced-motion` behave per UX-DR3  
**And** API errors from Nest are mapped to short French messages; non-invalid fields retain values on failure (UX-DR12, FR29).

### Story 1.6: Frontend — Authenticated shell, navigation, profile page, and sign out

As an authenticated user,  
I want a persistent sidebar, profile access, and sign out,  
So that I can move toward invoicing tasks with clear navigation.

**Acceptance Criteria:**

**Given** an authenticated user  
**When** they land in the app after login  
**Then** the shell shows Direction 1: left sidebar with Factures, Clients, Prestations, Profil vendeur (routes may 404 or placeholder until later epics), active state, account/sign-out, `main`/`nav` landmarks (UX-DR4, UX-DR17 touch targets)  
**And** the seller profile page uses the profile API from Story 1.4 with validation feedback (FR5, FR3 via sign out control)  
**And** ThemeSwitcher does not reset form state when used (UX-DR10).

---

## Epic 2: Client Directory

Users maintain a private client directory for invoicing.

### Story 2.1: Backend — Client entity and CRUD API with tenant isolation

As an authenticated user,  
I want my clients stored and accessed only under my account,  
So that no other user can see or change them.

**Acceptance Criteria:**

**Given** PostgreSQL and migrations adding `clients` with `userId`  
**When** I create, list, read, update, and delete clients via `/api/v1/clients`  
**Then** every query filters by authenticated `userId` and cross-tenant access returns 404 (FR7–FR10, FR4, NFR6)  
**And** required fields (name, email, company, address) are validated with unified errors (FR29)  
**And** unit tests prove isolation between two users.

### Story 2.2: Frontend — Client list and empty state

As an authenticated user,  
I want to see my clients or a helpful empty state,  
So that I know how to add the first one.

**Acceptance Criteria:**

**Given** the user opens the Clients section  
**When** they have no clients  
**Then** `ResourceEmptyState` shows French headline, one sentence, and a primary CTA to create (UX-DR9, UX-DR20)  
**When** they have clients  
**Then** the list loads with skeleton or spinner (UX-DR15) and supports navigation to detail/edit (UX-DR16).

### Story 2.3: Frontend — Create and edit client

As an authenticated user,  
I want to create and edit a client,  
So that my directory stays accurate.

**Acceptance Criteria:**

**Given** client forms  
**When** the user submits valid data  
**Then** success is confirmed (toast or inline per UX-DR15) and the list updates  
**When** validation fails  
**Then** field-level messages, `aria-invalid`, and preserved valid fields apply (UX-DR12, FR29)  
**And** one clear primary action per view (UX-DR14).

### Story 2.4: Frontend — Delete client with confirmation

As an authenticated user,  
I want to delete a client with confirmation,  
So that I do not remove data by mistake.

**Acceptance Criteria:**

**Given** a client row or detail  
**When** the user chooses delete  
**Then** a Radix `Dialog` asks confirmation in French with destructive styling (UX-DR14, FR10)  
**And** API errors map to French feedback; list refreshes on success.

---

## Epic 3: Billable Services (Prestations)

Users maintain reusable services with hourly HT rates.

### Story 3.1: Backend — Service entity and CRUD API

As an authenticated user,  
I want services stored with correct monetary precision,  
So that invoice lines can reuse reliable rates.

**Acceptance Criteria:**

**Given** a `services` (or `prestations`) table with `userId`, title, hourly rate in decimal/numeric (no float)  
**When** CRUD operations run on `/api/v1/services` (or chosen plural)  
**Then** tenant isolation matches Story 2.1 patterns (FR11–FR14, FR4, NFR6, architecture money rule).

### Story 3.2: Frontend — Service list and empty state

As an authenticated user,  
I want to list services or see an empty state,  
So that I add prestations before invoicing.

**Acceptance Criteria:**

**Given** the Prestations section  
**When** the catalog is empty  
**Then** `ResourceEmptyState` guides creation (UX-DR9)  
**When** services exist  
**Then** hourly rates display with EUR formatting and tabular figures (UX-DR11, UX-DR19).

### Story 3.3: Frontend — Create and edit service

As an authenticated user,  
I want to create and edit a service,  
So that titles and rates stay current.

**Acceptance Criteria:**

**Given** service forms in French  
**When** submitting valid data  
**Then** feedback follows UX-DR12, UX-DR14, UX-DR15  
**And** invalid rate or title shows field errors without clearing unrelated inputs (FR29).

### Story 3.4: Frontend — Delete service with confirmation

As an authenticated user,  
I want to delete a service safely,  
So that obsolete offerings are removed.

**Acceptance Criteria:**

**Given** a delete action  
**When** confirmed in a destructive dialog  
**Then** the service is removed and the list updates (FR14, UX-DR14).

---

## Epic 4: Invoices — Lines, Amounts, and Status

Users create invoices with lines, see HT/VAT/TTC in EUR, and manage lifecycle per transition rules.

### Story 4.1: Backend — Create draft invoice linked to one client

As an authenticated user,  
I want to create an invoice draft for a client,  
So that I can add lines next.

**Acceptance Criteria:**

**Given** `invoices` with `userId`, `clientId` referencing the user’s client, status default `draft`  
**When** `POST` create invoice with valid `clientId`  
**Then** the invoice belongs to the current user only; foreign keys reject other users’ clients (FR15, FR4, FR20 initial state)  
**And** OpenAPI/DTOs document the payload.

### Story 4.2: Backend — Invoice lines and persisted HT/VAT/TTC

As an authenticated user,  
I want lines and totals stored consistently,  
So that the UI and PDF use one source of truth.

**Acceptance Criteria:**

**Given** invoice lines referencing services and quantities (and VAT rules defined for V1)  
**When** lines are added, updated, or removed while status allows editing  
**Then** HT, VAT, and TTC are computed server-side and persisted in decimal-safe types; totals reconcile with line sums (FR16, FR17, FR18, FR23, FR28)  
**And** attempts to edit when status forbids it return a clear error with unified shape (FR20 prerequisite).

### Story 4.3: Backend — Invoice list, detail, and status transitions

As an authenticated user,  
I want to list and open invoices and change status per rules,  
So that I can run the billing lifecycle.

**Acceptance Criteria:**

**Given** a documented status transition matrix (draft/sent/paid/cancelled)  
**When** the user lists and fetches invoices and PATCHes status  
**Then** illegal transitions are rejected with explicit errors; legal ones persist (FR20, FR21, FR22)  
**And** `GET` by id returns 404 for other users’ invoices (FR4).

### Story 4.4: Frontend — Invoice list, empty state, and navigation

As an authenticated user,  
I want to browse invoices with sorting basics,  
So that I find documents quickly.

**Acceptance Criteria:**

**Given** the Factures section  
**When** no invoices exist  
**Then** `ResourceEmptyState` points to creating an invoice (UX-DR9, UX-DR21)  
**When** invoices exist  
**Then** minimal sort by date or status is available if scoped (UX-DR16) and rows link to detail routes (deep links) (UX-DR16, FR21).

### Story 4.5: Frontend — Invoice editor shell, client selection, draft save, and prerequisite prompts

As an authenticated user,  
I want to start an invoice and save a draft,  
So that I can complete lines afterward.

**Acceptance Criteria:**

**Given** create/edit invoice page  
**When** seller profile or client is missing  
**Then** inline prompts guide to Profil vendeur or Clients before blocking (UX-DR21)  
**When** the user picks a client and saves  
**Then** layout follows UX-DR5 at `lg+` (two columns reserved for lines + sticky summary) and stacks below `lg`  
**And** French copy, primary hierarchy, and error mapping apply (UX-DR12, UX-DR14, FR15, FR29).

### Story 4.6: Frontend — Invoice line table, service picker, and totals panel

As an authenticated user,  
I want to edit lines and see HT/VAT/TTC from the server,  
So that I trust amounts before PDF.

**Acceptance Criteria:**

**Given** `InvoiceLineTable` / editor and `InvoiceTotalsPanel` with heading “Synthèse” (UX-DR7)  
**When** the user adds/removes/reorders lines, picks a service for prefilled description and unit HT, and enters quantities  
**Then** displayed amounts use `MoneyDisplay` with tabular nums and EUR (UX-DR6, UX-DR11)  
**And** read-only mode applies when status disallows edits, with helper text (UX-DR6, FR17)  
**And** short inline help explains snapshot semantics if backend copies catalog values at line creation (UX-DR22)  
**And** after save/refetch, totals match API (FR19, FR23, UX-DR5).

### Story 4.7: Frontend — Invoice status actions

As an authenticated user,  
I want actions that match my invoice status,  
So that I only perform allowed transitions.

**Acceptance Criteria:**

**Given** `InvoiceStatusActions`  
**When** the invoice loads  
**Then** only allowed transitions render as enabled; disabled actions expose tooltip or helper explaining why (UX-DR8, FR20)  
**And** destructive actions (e.g. cancel) use confirmation dialog in French (UX-DR8, UX-DR14)  
**And** status badge shows text plus color not color alone (UX-DR18)  
**And** mutations show loading and toasts on success (UX-DR15).

---

## Epic 5: Invoice PDF and Document Parity

Users download PDFs that mirror persisted invoices.

### Story 5.1: Backend — PDF generation from persisted invoice

As an authenticated user,  
I want the server to build my PDF from stored data,  
So that the file matches what I saved.

**Acceptance Criteria:**

**Given** Nest PDF module (e.g. HTML template + Puppeteer or PDFKit)  
**When** `GET` or `POST /api/v1/invoices/:id/pdf` is called with a valid token  
**Then** the invoice is loaded by id and `userId`; other users get 404  
**And** the PDF includes seller, client, date, lines, totals per V1 minimum set (FR24, FR26, FR27, FR6)  
**And** amounts match persisted values in automated tests (parity)  
**And** idempotency expectations are documented (architecture).

### Story 5.2: Frontend — PDF trigger, loading state, and download

As an authenticated user,  
I want visible progress and a reliable download,  
So that I know generation succeeded.

**Acceptance Criteria:**

**Given** the invoice detail page  
**When** the user triggers PDF generation  
**Then** the button shows “Génération en cours…” or equivalent and disabled/loading state (UX-DR13, NFR3)  
**And** success enables download or opens blob; failure shows retry and French error (UX-DR13, UX-DR12)  
**And** `aria-live` (or equivalent) announces result for assistive tech (UX-DR13, UX-DR18)  
**And** primary button hierarchy respected (UX-DR14).

---

## Epic 6: Quality, Deployment, and Traceability

Stakeholders can run, verify, and grade the shipped product with documented evidence.

### Story 6.1: Dockerfiles and local full stack

As a developer,  
I want Dockerfiles and compose (or documented equivalent) for frontend and backend,  
So that anyone can run the full stack locally.

**Acceptance Criteria:**

**Given** each repository  
**When** images build and compose brings up API, UI, and database  
**Then** README documents ports, env vars, and `docker compose up` (or equivalent)  
**And** secrets use `.env.example` only—no secrets in git (NFR5, architecture).

### Story 6.2: CI/CD pipelines and environments

As a team member,  
I want pipelines that lint, test, build, and deploy,  
So that staging and production stay predictable.

**Acceptance Criteria:**

**Given** GitHub Actions (or chosen CI) per repo  
**When** pipeline runs on relevant branches  
**Then** steps include install, lint, format check, tests, build, Docker image build, publish, deploy to **staging** and **production** per team mapping  
**And** branch strategy `feature/`* → `develop` → `staging` → `main` is documented (PRD technical success).

### Story 6.3: Load test on staging with recorded metrics

As a DevOps engineer,  
I want load-test results before production promotion,  
So that we meet course and NFR expectations.

**Acceptance Criteria:**

**Given** staging URL  
**When** the team runs an agreed load scenario  
**Then** throughput, latency percentiles, and error rate are recorded and attached to the release gate (NFR2, NFR1, NFR8).

### Story 6.4: README, OpenAPI visibility, release/version identifier, GDPR and scope honesty

As a reviewer,  
I want URLs, API docs, version, and privacy stance,  
So that I can validate the assignment quickly.

**Acceptance Criteria:**

**Given** production (and backend) URLs in README  
**When** the reviewer opens Swagger/OpenAPI  
**Then** `/api/v1` resources are documented (FR31 support)  
**And** the deployed UI or docs expose a version/build identifier (FR31)  
**And** README or linked notice states GDPR-oriented minimization, retention/deletion stance, and **does not** claim legal certification beyond implemented invoice fields (FR30, NFR7).

### Story 6.5: Cross-cutting verification — HTTPS, errors, and accessibility on primary routes

As a team,  
I want baseline security and accessibility verified,  
So that NFR4 and NFR10 are satisfied for V1.

**Acceptance Criteria:**

**Given** staging/production hosts  
**When** traffic is inspected  
**Then** HTTPS is enforced for browser traffic (NFR4)  
**And** global Nest exception filter yields unified JSON errors if not already complete in earlier stories  
**And** primary routes (login, invoice detail) pass agreed checks (manual, axe, or Lighthouse as team defines) with focus visible and keyboard order sane (NFR10, UX-DR18).

---

## Rapport de validation finale (étape 4 — BMAD Create Epics and Stories)

*Validation effectuée contre l’inventaire d’exigences et les règles du workflow ; aucun placeholder `{{…}}` restant dans ce document.*

### 1. Couverture des FR


| Statut | Détail                                                                                                                                                     |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OK     | FR1–FR31 chacun mappé dans la carte de couverture et adressé par au moins une story ( références explicites dans les critères d’acceptation où pertinent). |


### 2. Conformité architecture / starters


| Statut | Détail                                                                                                                                                                                                                                                                                                                                         |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OK     | Les starters officiels **NestJS** et **Next.js** sont couverts : **Story 1.1** (API Nest + PostgreSQL + modèle `User` + inscription) et **Story 1.5** (bootstrap Next, tokens, écrans auth). L’intitulé 1.1 regroupe bootstrap backend et première capacité métier pour respecter le principe « tables / entités seulement quand nécessaire ». |
| Note   | **OpenAPI**, garde-fous tenant, PDF côté Nest, erreurs JSON unifiées : répartis dans les stories 1.x, 4.x, 5.1, 6.5.                                                                                                                                                                                                                           |


### 3. Création incrémentale des données


| Statut | Détail                                                                                                                                                     |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OK     | `User` → **1.1** ; `clients` → **2.1** ; `services` → **3.1** ; `invoices` / lignes → **4.1–4.2** ; pas de création « toutes les tables » en story unique. |


### 4. Qualité des stories


| Statut | Détail                                                                                                                                    |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------- |
| OK     | Format user story + Given/When/Then ; périmètre compatible avec une session dev agent ; détails techniques (API, isolation, UX) présents. |


### 5. Structure des epics et dépendances


| Statut | Détail                                                                                                                                                                                             |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OK     | Epics centrés **valeur utilisateur** (pas « couche API seule »). Enchaînement logique : 2 et 3 indépendants entre eux après 1 ; 4 consomme 2–3 ; 5 consomme 4 ; 6 orthogonal (livraison / preuve). |
| OK     | Au sein de chaque epic, les stories **n.2** s’appuient sur **n.1**, etc., sans dépendre d’une story **future** du même epic.                                                                       |


### 6. Exigences UX (UX-DR)


| Statut | Détail                                                                                                                                                       |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| OK     | UX-DR1–UX-DR23 couverts par les stories des epics 1–6 (shell, thèmes, facture, PDF, listes vides, accessibilité, etc.), comme indiqué dans la liste d’epics. |


**Verdict :** prêt pour la mise en œuvre (sous réserve des choix produit à trancher en cours de dev : matrice de statuts facture, champs légaux V1, Prisma vs TypeORM, CORS direct vs BFF).