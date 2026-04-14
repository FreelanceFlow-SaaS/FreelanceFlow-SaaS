---
stepsCompleted:
  - step-01-init
  - step-02-discovery
  - step-02b-vision
  - step-02c-executive-summary
  - step-03-success
  - step-04-journeys
  - step-05-domain
  - step-06-innovation
  - step-07-project-type
  - step-08-scoping
  - step-09-functional
  - step-10-nonfunctional
  - step-11-polish
  - step-12-complete
workflowStatus: complete
prdCompletedAt: '2026-03-27'
inputDocuments:
  - docs/presentation.md
  - docs/v2.md
date: '2026-04-14'
lastEdited: '2026-04-14'
editHistory:
  - date: '2026-04-14'
    summary: >-
      Edit PRD: added V2 scope from docs/v2.md—invoice email with PDF attachment,
      CSV exports (clients, invoices), enriched dashboard KPIs, PDF logo upload,
      application caching for hot paths, structured logging and a reachable
      monitoring view; recorded V2 delivery constraints (architecture fit, tests,
      CI/CD, deployment, backlog hygiene, releases/tags).
  - date: '2026-04-14'
    summary: >-
      Post-validation edit: deduplicated V2 narrative (Product Scope points to
      phased section); added Measurement & operational definitions appendix;
      reduced implementation examples in NFR-C2/O1/O2; clarified NFR-I2 retries;
      set prd date frontmatter for completeness checks.
prdEditWorkflow:
  stepsCompleted:
    - step-e-01-discovery
    - step-e-02-review
    - step-e-03-edit
documentCounts:
  brief: 0
  research: 0
  brainstorming: 0
  projectDocs: 2
workflowType: 'prd'
classification:
  projectType: saas_b2b
  domain: general
  complexity: medium
  projectContext: greenfield
  compliance:
    jurisdiction: France
    focus: "Invoicing and VAT rules aligned with French law; mandatory invoice fields; GDPR for personal data."
  notes: "SaaS delivery model; V1 scope per course brief; V2 extension per docs/v2.md (email invoices, exports, dashboard KPIs, PDF branding, cache, observability); French legal/fiscal adaptation is a core product constraint."
---

# Product Requirements Document - FreelanceFlow - SaaS

**Author:** Dredjib
**Date:** 2026-03-27

**Inputs:** Assignment brief `docs/presentation.md`; frontmatter `classification` (SaaS `saas_b2b`, France, greenfield, medium complexity).

## Executive Summary

FreelanceFlow is a **French-market SaaS** that lets a **solo freelancer** manage **clients**, **services (prestations)** with hourly rates, and **invoices** end-to-end in the browser. It replaces ad hoc stacks (spreadsheets, static templates, disconnected tools) with a **single hosted workflow**: authenticate, maintain a client directory, attach services to invoices, compute **HT / TVA / TTC**, track **invoice status** (draft, sent, paid, cancelled), and **generate downloadable PDFs** built from live application data. The product is intentionally scoped to a **clear V1** (per the course brief): working software, reproducible delivery (**Docker**, **CI/CD**), and a **production URL**—not feature sprawl.

**Target users:** freelancers who need **credible, repeatable invoicing** without running a second job as a tax lawyer—while still operating under **French invoicing and VAT expectations** and **EU GDPR** for personal data.

**Problem:** fragmented tools and manual documents increase errors, slow billing, and weaken trust when invoice content does not match how work was actually sold and delivered.

**Vision of success:** the freelancer can run **commercial activity**—from client record to issued PDF—with **defaults and structures aligned to France**, in one place, always up to date as a **deployed SaaS**.

**V2 extension (see `docs/v2.md`):** the same product gains **operational depth**—**send invoices by email** with the generated PDF attached, **export** client and invoice lists to **CSV**, an **enriched dashboard** (total revenue, invoice count, revenue by client and by month, computed live from stored data), **PDF branding** via an **uploaded logo**, **caching** to reduce redundant work on dashboards and lists, and **structured logging plus a simple monitoring surface** (health, errors, optional metrics) reachable by the team—without breaking **existing architecture**, **tests**, **CI/CD**, or **production availability**.

### What Makes This Special

- **Jurisdiction-first invoicing:** structure and outputs are designed for **French legal and fiscal context** (VAT logic, mandatory invoice elements, data retention mindset, EUR)—not a generic international invoice toy.
- **SaaS delivery:** one **online product** with professional **build, test, container, and deploy** practices—matching both real freelance use and the **pedagogical CI/CD** requirements.
- **Honest V1:** depth on **clients, prestations, factures, PDF** and quality bar (lint, tests, pipeline) over speculative features—**coherence and ship** over ambition without stability.

## Project Classification

| Dimension | Value |
|-----------|--------|
| **Product type** | SaaS (BMAD: `saas_b2b`) — web-delivered, multi-user product pattern; V1 targets an individual freelancer account scope per assignment. |
| **Domain** | General business operations / freelance administration (BMAD: `general`). |
| **Complexity** | **Medium** — product scope is contained, but **French invoicing + VAT + GDPR** raise compliance and correctness expectations beyond a trivial CRUD demo. |
| **Project context** | **Greenfield** — new application defined by the assignment; existing input doc is **specification**, not a legacy system to extend. |
| **Compliance** | **France:** invoicing and VAT alignment, mandatory invoice fields; **EU/FR:** GDPR for personal data. |

## Success Criteria

### User Success

- A freelancer can **register and sign in**, then **create and manage clients** (name, email, company, address) without data loss or confusing errors.
- The user can **define services** (title, hourly rate) and **create an invoice** linking **one client** and **one or more services**, with **HT, VAT, and TTC computed automatically** and **invoice status** set and updated (draft, sent, paid, cancelled).
- The user can **generate and download a PDF invoice** whose content **reflects application data** (freelancer identity, client, date, line items, totals)—minimum set per assignment—and is **credible for a French context** (EUR, VAT treatment aligned with product rules, mandatory fields the product commits to support in V1).
- The user experiences **clarity**: primary flows are completable in a **single session** without workarounds; critical actions surface **clear feedback** (success, validation errors).

### Business Success

- **Course / delivery success:** repository shared with the instructor, **GitHub Projects** backlog with assignees and progress, **documented production URLs** (frontend; backend if applicable), **README** with stack, URLs, and team—matching evaluation expectations.
- **Product credibility:** evaluators can verify **end-to-end utility** (not only screenshots): live app demonstrates **V1 scope** with coherent UX and **French-oriented** invoicing posture.
- **Team sustainability:** roles (frontend, backend, DevOps) map to **reviewable PRs** linked to issues; **releases and tags** (e.g. v1.0.0) prove a controlled ship.

### Technical Success

- **Quality gates:** **lint**, **Prettier**, **pre-commit hooks** enforced locally and in **CI**.
- **Testing:** **mandatory backend unit tests**; **load test** with reported **metrics**; tests run in **CI** on every relevant pipeline execution.
- **Containerization:** **Dockerfile** for frontend and backend; **docker-compose** (or equivalent) runs the **full stack locally** reproducibly.
- **CI/CD:** pipeline runs **dependency install**, **lint**, **format check**, **tests**, **build**, **Docker image build**, **registry publish**, **automated deployment** to the chosen host.
- **Git workflow:** `feature/*` → `develop` → **`staging`** → `main`. The **`staging`** branch/environment is used for **pre-production validation** (including **load tests** and other checks) before promoting to **production** (`main`).
- **Compliance-oriented engineering:** **GDPR-aware** handling of personal data (minimization, lawful basis documented at least at product/README level); **invoice/PDF rules** implemented consistently with stated French-market scope.
- **V2 engineering:** **automated tests** cover new domains (email dispatch edge cases, export shape, dashboard aggregation, cache invalidation hooks where feasible); **observability** configuration is **documented** for operators (where logs go, how to open monitoring); **performance** of cached endpoints remains within **team budgets** documented next to the load-test artifacts.

### Measurable Outcomes

| Area | Indicator |
|------|-----------|
| Core flow | **100%** of V1 functional requirements from the assignment are demonstrable in **production** (auth, clients, prestations, invoices + statuses, PDF). |
| Quality bar | **CI green** on default branch for lint + format + tests + build + Docker steps defined in the pipeline. |
| Performance | **Load test** documented with **metrics** (e.g. latency/throughput thresholds agreed by the team and recorded). |
| Delivery | **Tagged release** (e.g. **v1.0.0**) and **GitHub release** published; **two working URLs** (frontend + backend if split) reachable. |
| Data trust | PDF output **matches** stored invoice data (no static mock content for amounts or client identity). |

### V2 Success (product extension)

| Area | Indicator |
|------|-----------|
| Email delivery | User can **send** an invoice **by email** with a **custom message** and the **current PDF attached**; failures surface **actionable** feedback (e.g. provider/config). |
| Exports | User can **download CSV** for **all own clients** and **all own invoices** with fields useful for spreadsheet use and consistent encoding. |
| Dashboard | Dashboard shows **total revenue**, **total invoice count**, **revenue by client**, and **revenue by month**, all **derived dynamically** from persisted invoices (no hard-coded demo numbers). |
| PDF branding | User can **upload** a logo; **generated PDFs** include that logo **without** breaking mandatory invoice content or layout readability. |
| Performance | **Cached or equivalent** optimizations measurably reduce duplicate heavy reads for **dashboard** and **list** views under team-defined invalidation rules. |
| Operations | **Structured logs** support troubleshooting; a **monitoring dashboard or page** (tooling choice open) shows **application health**, **errors**, and **optional metrics**; **staging/production** remain deployable and **CI/CD** stays green. |
| Delivery hygiene | V2 ships with **clean backlog** traceability, **release notes**, and a **tagged release** plan (e.g. **v2.0.0**) consistent with V1 release discipline. |

## Product Scope

### MVP - Minimum Viable Product

Aligned with **V1** in `docs/presentation.md` and **French-market** constraints in this PRD. Phased roadmap, delivery gates, and risk mitigations: see **Project Scoping & Phased Development**.

- **Authentication:** sign-up, sign-in.
- **Clients:** create, list, update, delete; fields: name, email, company, address.
- **Prestations:** title + hourly rate.
- **Invoices:** create from client + one or more services; **HT / TVA / TTC** automatic; **status** (draft, sent, paid, cancelled).
- **PDF:** generate and download; dynamic content including freelancer info, client, date, lines, totals; **France-aligned** rules the product explicitly implements in V1.
- **Engineering:** Docker, CI/CD, tests, lint/format, pre-commit, deployment, README, API documentation (per assignment).

### V2 - Product extension (per `docs/v2.md`)

Ship **after V1** as a coherent increment. Capabilities match **`docs/v2.md`** and **FR32–FR43** / **NFR-I2** onward. **Must-have wording, phased sequencing, journey coverage (Camille), and delivery gates** are specified once under **Project Scoping & Phased Development → V2 Feature Set (Phase 2)**—read that subsection as the **canonical checklist** for implementation planning.

**Product intent (summary):** send invoices **by email** with **PDF** attachment; **CSV** export of **clients** and **invoices** (tenant-scoped); **dashboard** KPIs for total revenue, invoice count, revenue by client and by month from stored data; **logo** on PDFs; **caching** for dashboard and lists with documented freshness rules; **structured logs** and a **reachable monitoring** surface in staging and production.

**V2 delivery constraints:** fit **existing architecture**; maintain **tests** and **green CI/CD**; keep **staging/production** reachable; **backlog** and **tagged release** discipline as in the **V2 Success** table and **NFR-G1–G2**.

### Growth Features (Post-V2 / Post-MVP)

- Richer **French invoicing** (e.g. additional legal mentions by legal form, credit notes, deposit invoices).
- **Dashboard extensions** not required by V2 (e.g. **overdue** aging, cash forecast).
- **Integrations** beyond V2 email (accounting tools, e-invoicing platforms), **multi-user org** / assistant roles.
- **E-invoicing** / platform connectivity if required by future regulation and scope.

### Vision (Future)

- **Durable SaaS** for solo freelancers in France: **reliable billing**, **traceability**, optional **expansion** to broader freelance operations while keeping **compliance** and **simplicity** as differentiators.

## User Journeys

### 1. Sophie — Primary user (happy path)

**Persona:** Sophie, **freelance developer** in France. She is tired of spreadsheets and Word templates for quotes and invoices; she wants **one reliable place**, **aligned with the French context**, without VAT mistakes.

**Opening:** On a Monday morning she **creates an account** on FreelanceFlow and **signs in**. She adds her **first client** (local SME) with name, email, company, address. She creates two **reusable services** (“Web development”, “Maintenance”) with **hourly rates**.

**Rising action:** She **creates an invoice**: selects the client, adds lines from services, quantities/hours. The app shows **HT, VAT, and TTC** without manual calculation. She keeps the invoice in **draft**, checks amounts, then sets status to **sent** when she dispatches it—**V1:** typically via **email outside** the app; **V2:** she may **send from FreelanceFlow** with the **PDF attached** and a short message.

**Climax:** She clicks **Generate PDF**: the document includes **her details**, **the client’s**, **date**, **lines**, **totals**—consistent with the UI. She **downloads** the file to archive and send.

**Resolution:** She completes **client → invoice → PDF** in one session, trusting that amounts and required fields match the **France / EUR** scope the product commits to.

**Failure & recovery:** If validation fails (required field), she sees a **clear message** and fixes it without losing the rest of the form.

---

### 2. Mehdi — Primary user (edge case & correction)

**Persona:** Mehdi, **consultant**. He invoiced a line too early and must **correct before archiving**.

**Opening:** An invoice is already **sent**; he notices a **quantity** error on one line.

**Rising action:** Depending on V1 business rules, he either **returns to draft** if allowed, or **cancels** and **recreates** a corrected invoice (implementation must define **status transition** rules). He updates lines; **HT / VAT / TTC** **recalculate**.

**Climax:** The **regenerated PDF** reflects **exactly** the corrected data—no mismatch between screen and file.

**Resolution:** He can justify billing to the client without contradictory “two versions.”

**Emotional arc:** Anxiety (error) → control (status + recalculation) → relief (PDF aligned).

---

### 3. Léa — Team member (DevOps / delivery)

**Persona:** Léa, **DevOps** in the four-person team. Success means a **green pipeline** and **predictable deployment**.

**Opening:** PRs merge `feature/*` → `develop`.

**Rising action:** A release candidate is promoted to **`staging`**: a **pre-production** environment where the team runs the **load test** (metrics retained) and other checks (smoke, regression, performance as defined by the team).

**Climax:** After validation on **`staging`**, promotion to **`main`** means **production**; images/version align with the tagged release.

**Resolution:** Production only receives what was **validated under load** and **verified** on `staging`.

**Capabilities implied:** pipeline that can deploy at least **staging** and **production**; load-test metrics **attached** to the `staging` gate; clear **promotion** rules (who merges what, when).

---

### 4. Instructor / evaluator — Acceptance journey

**Persona:** Instructor or grader for the **CI/CD project**.

**Opening:** They open the **README**, **GitHub** link, **GitHub Projects**, then **production URLs**.

**Rising action:** They verify **auth**, **client CRUD**, **services**, **invoices** + **statuses**, **PDF**; they check **quality** (lint, tests, Docker, CI).

**Climax:** The thread **issue → PR → merge → deploy** is **readable**; the product is **usable**, not only a local demo.

**Resolution:** Assignment **grading criteria** are **verifiable** in under ~one hour of structured exploration.

---

### 5. Camille — Operations & insight (V2 path)

**Persona:** Camille, **freelance designer**. She already uses FreelanceFlow for **V1** flows; she wants **less copy-paste** and a **clear picture of revenue**.

**Opening:** She uploads her **studio logo** once; new **PDFs** show it next to her invoice header.

**Rising action:** After marking invoices **paid**, she opens the **dashboard** and sees **total revenue**, how many **invoices** she has issued, which **clients** contributed most, and **month-by-month** revenue—all **computed from her data**. She **exports clients and invoices to CSV** for her accountant’s template.

**Climax:** For a large invoice she uses **Send by email**: she adds a short **message**, confirms, and the client receives the **same PDF** she would have downloaded—**no duplicate amounts**.

**Resolution:** When the team checks **monitoring**, they see **healthy** instances and can **trace errors** from structured logs after a failed send or slow PDF.

**Capabilities implied:** transactional email with attachment; CSV generation; aggregate queries with caching; logo storage and PDF template update; observability UI + structured logs.

---

### Journey Requirements Summary

| Journey | Capabilities implied |
|--------|----------------------|
| Sophie (happy path) | Auth onboarding; client & service CRUD; multi-line invoices; HT/VAT/TTC; statuses; PDF generation/download; usable error messages. |
| Mehdi (edge) | Explicit **status transition** rules; reliable **recalculation**; **PDF ↔ data** consistency; clear **cancel/correct** path. |
| Léa (DevOps) | Full **CI/CD**; Docker; registry; deploy **staging** then **prod**; `develop` → **`staging`** → **`main`** with **load test** on staging; release/tag. |
| Evaluator | README; links; project traceability; API documentation; **production** end-to-end proof. |
| Camille (V2) | Email send w/ PDF; CSV exports; dashboard KPIs; logo on PDF; cache-backed lists/dashboard; logs + monitoring surface. |

**Out of scope for V1 (unless added later):** end-customer portal in the app, third-party support desk, accounting integrations—not required by the initial brief.

## Domain-Specific Requirements

### Compliance & regulatory (France & EU)

- **Invoicing (France):** V1 must document which **mandatory invoice fields** the product enforces (seller/buyer identity, invoice id, dates, HT/TVA/TTC breakdown in line with product rules, currency **EUR**). Legal form–specific mentions (e.g. SIREN/SIRET, VAT number, RCS) should be **explicitly in or out of scope** for V1 and tracked as backlog if deferred.
- **VAT:** Support **French VAT logic** as defined in product rules (rates, exempt/excluded cases if any); calculations must be **auditable** (stored line amounts, not only PDF).
- **Document retention:** Align with **French accounting retention expectations** for invoices (product should not block export/deletion policies that contradict legal retention—document behavior for **delete** vs **archive**).
- **GDPR (EU/FR):** Lawful basis, **purpose limitation**, **data minimization** for client/freelancer PII; **access** and **erasure** requests considered in roadmap; **security** of personal data (transport + storage); **privacy notice** pointer in README or in-app as appropriate for V1.

### Technical constraints

- **Security:** Authenticated access to resources; **no cross-tenant data leakage** (single freelancer per account scope in V1—still enforce **user isolation** in design).
- **Integrity:** Invoice and PDF **must match** persisted data; **immutable or controlled edits** after issue depending on status rules.
- **Auditability:** Backend stores enough detail to **reconstruct** HT/TVA/TTC and invoice status history if required for support or grading.

### Integration requirements

- **V1:** No mandatory external integration (tax authority portals, accounting exports) unless the team adds them; **PDF** is the primary **export** artifact.

### Risk mitigations

| Risk | Mitigation |
|------|------------|
| Product claims “French-compliant” without legal review | Label outputs as **aligned with implemented rules**; document **limitations**; avoid over-claiming in README. |
| Wrong VAT or totals | Unit tests on **calculation**; manual QA matrix for rates; PDF **parity checks**. |
| GDPR gaps | Minimal PII fields; clear **retention** story; secure defaults (HTTPS, secrets not in repo). |

## SaaS B2B Specific Requirements

### Project-Type Overview

FreelanceFlow is classified as **`saas_b2b`** in BMAD: a **hosted, browser-based product** with accounts and persisted data. The **course V1** targets a **solo freelancer** per account—not a multi-seat enterprise tenant—so classic B2B patterns (orgs, RBAC matrices, pricing tiers) are **simplified or deferred** while preserving **strong tenant isolation** at the **user/account** level.

### Technical Architecture Considerations

- **Hosting:** Public SaaS deployment (staging + production); HTTPS; secrets outside source control.
- **Data isolation:** All domain entities (clients, services, invoices) are **scoped to the authenticated account**; APIs must enforce **authorization by resource owner** (no cross-account reads/writes).
- **State & consistency:** Invoice totals and PDF generation must use **server-side truth** (same source as PDF), not client-only calculations.

### tenant_model

| Aspect | V1 decision |
|--------|-------------|
| **Model** | **Single-user logical tenant** per registered freelancer: one **account id** partitions all data. |
| **Multi-tenant (strict sense)** | No **organization** or **sub-accounts** in V1; future multi-user orgs are **out of scope** unless explicitly added later. |
| **Isolation** | Database queries and API handlers always filter by **authenticated user / account**; tests must cover **forbidden cross-tenant access**. |

### rbac_matrix

| Role | V1 scope |
|------|----------|
| **Account owner** | Full CRUD on own clients, services, invoices; PDF generation; profile/settings as implemented. |
| **Other roles** | **None** in V1 (no admin, viewer, or billing contact roles). |

### subscription_tiers

- **V1:** **Not in scope** for the academic product (no paid plans, feature flags, or metering required by the assignment). Document as **single implicit tier** (full app access for authenticated user).
- **Post-MVP:** Optional tiers, trials, or limits if the product evolves commercially.

### integration_list

- **V1:** **No required** third-party integrations (payment gateways, accounting, e-invoicing platforms). **PDF download** is the primary handoff artifact.
- **V2:** **Transactional email** provider (or equivalent) to **send invoice emails** with **PDF attachment**; credentials and quotas managed outside source control.
- **Post-V2:** Accounting system connectors, Chorus/e-invoicing—**backlog** unless promoted.

### compliance_reqs

- **Cross-reference:** See **Domain-Specific Requirements** (France invoicing, VAT, GDPR). For SaaS operations: secure transport, minimal PII, documented retention and deletion stance.
- **Product claims:** Marketing/README must align with **implemented** fields and rules, not imply certification or legal advice.

### Implementation Considerations

- **Skip (per CSV):** No **CLI** product surface; no **mobile-first** mandate—responsive web is sufficient.
- **Observability:** **V1:** logging/monitoring sufficient to debug production and satisfy grading (errors, deploy health); load-test metrics tied to **staging** gate (see Technical Success / User Journeys). **V2:** **structured logs** and a **reachable monitoring view** per **V2 Feature Set** and **Non-Functional Requirements**.
- **API surface:** REST (or equivalent) documented for graders; versioning strategy **lightweight** for V1 if needed (e.g. `/api/v1/...`).

## Project Scoping & Phased Development

### MVP Strategy & Philosophy

**MVP Approach:** **Problem-solving + platform MVP** — ship the **smallest end-to-end slice** that proves value: **auth → clients → prestations → invoice (HT/TVA/TTC, statuses) → PDF**, deployed via **develop → staging → main** with **CI/CD**, **Docker**, **tests**, and **documented URLs**. The “platform” is the **reproducible delivery pipeline**, not feature breadth.

**Resource Requirements:** **Team of 4** (frontend, backend, DevOps focus, collective ownership). Minimum skills: web stack chosen by team, API design, basic security, containerization, pipeline authoring, French-market **rules as implemented** (no legal counsel assumed).

### MVP Feature Set (Phase 1)

**Core User Journeys Supported:**

- **Sophie (happy path):** signup/login → client CRUD → service definitions → invoice with lines → statuses → PDF download.
- **Mehdi (edge):** status rules and correction path **as implemented** (draft/cancel/recreate or equivalent).
- **Léa:** automated pipeline, **staging** validation including **load test metrics**, promotion to **prod**.
- **Evaluator:** README, Projects, URLs, API doc, runnable **production** demo.

**Must-Have Capabilities:**

- Authentication (register, login).
- Client CRUD with required fields (name, email, company, address).
- Prestations (title, hourly rate).
- Invoice from client + one or more services; automatic HT, VAT, TTC; statuses: draft, sent, paid, cancelled.
- PDF generation and download; dynamic content (freelancer, client, date, lines, totals); **France-aligned** rules explicitly implemented in V1.
- Engineering: lint, Prettier, pre-commit, backend unit tests, load test + metrics, CI, Dockerfiles, compose, deploy, README, API documentation.
- Git: `feature/*` → `develop` → **`staging`** → `main`; PRs; release/tag (e.g. v1.0.0).

### V2 Feature Set (Phase 2 — per `docs/v2.md`)

**Canonical scope checklist** (aligns with **Product Scope → V2** and `docs/v2.md`; numeric defaults and revenue basis: **Measurement & operational definitions**).

- **Email invoice:** authenticated user sends **one invoice** to one or more **recipient addresses** with **message** + **PDF** attachment generated from current data.
- **CSV exports:** **clients** and **invoices** for the authenticated account (UTF-8; column set **frozen** in API or README per appendix).
- **Dashboard KPIs:** **total revenue**, **invoice count**, **revenue by client**, **revenue by month**—computed from persisted data per **appendix** revenue rules (same basis in UI and CSV).
- **Logo on PDF:** upload/store **logo**; include on **PDF** output without obscuring mandatory fields.
- **Caching:** for **dashboard** and **list** reads; **invalidate** or **TTL** per **appendix** and **NFR-C1**.
- **Observability:** **structured logs**; **monitoring** UI or linked tool for **liveness**, **errors** / error rate, **optional** metrics.

**Delivery gates:** same **quality bar** as V1 (lint, tests, CI/CD, Docker, deploy URLs); **tagged V2 release** (e.g. **v2.0.0**) with **release notes**; backlog items traceable to PRs/issues.

**Core User Journeys Supported (V2):**

- **Camille (V2):** logo on PDF → dashboard KPIs → CSV exports → send invoice by email with PDF → team uses monitoring/logs for incidents.

### Post-MVP Features

**Phase 3 (Growth beyond V2):**

- Richer French invoicing (credit notes, deposits, legal mentions by status).
- Dashboard extras (e.g. **overdue** views) not mandated by V2.
- Optional integrations (accounting); stricter GDPR workflows (export/erase self-service) beyond CSV export.

**Phase 4 (Expansion):**

- Multi-user org, roles, commercial subscriptions if productized.
- E-invoicing / regulatory connectivity as required by future law and scope.

### Risk Mitigation Strategy

**Technical Risks:** **Mitigation** — automated tests for **VAT/totals**; PDF **parity** checks; **staging** load tests before **main**; narrow scope to reduce integration surface.

**Market / course risks:** **Mitigation** — grader-visible **traceability** (issues, PRs, URLs); clear README; honest **limitations** on legal claims.

**Resource risks:** **Mitigation** — **cut features before cutting quality**; defer **Phase 3+** items; enforce **definition of done** per journey (Definition of Done aligned with success criteria).

## Functional Requirements

### Account & Access

- **FR1:** A visitor can create an account to use the product.
- **FR2:** A registered user can sign in.
- **FR3:** An authenticated user can end their session (sign out) when the product provides that control.
- **FR4:** An authenticated user can access only their own clients, services, and invoices (no cross-account access).

### Seller Profile (Invoice Identity)

- **FR5:** An authenticated user can maintain seller information used on invoices (identity and identifiers required by the implemented French-market rules in V1).
- **FR6:** The product can include the seller’s maintained information on invoices and PDFs when those fields are in scope for V1.

### Client Directory

- **FR7:** An authenticated user can create a client with name, email, company, and address.
- **FR8:** An authenticated user can view a list of their clients.
- **FR9:** An authenticated user can update an existing client’s information.
- **FR10:** An authenticated user can delete a client they no longer need.

### Service Catalog (Prestations)

- **FR11:** An authenticated user can create a service with a title and an hourly rate.
- **FR12:** An authenticated user can view a list of their services.
- **FR13:** An authenticated user can update an existing service.
- **FR14:** An authenticated user can delete a service.

### Invoicing & Totals

- **FR15:** An authenticated user can create an invoice associated with one client.
- **FR16:** An authenticated user can add one or more lines to an invoice, referencing defined services and quantities or hours as required by the product rules.
- **FR17:** An authenticated user can remove or adjust lines on an invoice when the invoice status allows editing.
- **FR18:** The product can compute HT, VAT, and TTC from invoice lines and rates according to the French-market VAT rules implemented in V1.
- **FR19:** An authenticated user can view HT, VAT, and TTC on the invoice before generating a PDF.
- **FR20:** An authenticated user can set or transition invoice status among the supported values (draft, sent, paid, cancelled) according to the product’s transition rules.
- **FR21:** An authenticated user can view a list of their invoices.
- **FR22:** An authenticated user can open and view the details of a single invoice.
- **FR23:** Stored invoice monetary amounts remain consistent with the line items and applied VAT rules (single source of truth for totals).

### Invoice PDF Output

- **FR24:** An authenticated user can generate a PDF for an invoice from current invoice data.
- **FR25:** An authenticated user can download the generated PDF file.
- **FR26:** A generated PDF can include the seller, the client, the invoice date, line items, and totals required by the minimum implemented set for V1.
- **FR27:** A generated PDF reflects the same invoice data as shown in the application for amounts, client identity, and line content.

### Compliance-Oriented UX (France / Data)

- **FR28:** The product can operate monetary amounts in EUR for V1.
- **FR29:** When required invoice or client fields are missing or invalid, the user can see clear validation feedback and correct the input.
- **FR30:** The product’s behavior and labels avoid claiming legal certification beyond what is implemented; limitations can be communicated via product copy or documentation as agreed by the team.

### Traceability for Stakeholders (Product Surface)

- **FR31:** A reviewer can identify the product version or release identifier exposed in the deployed application or documentation (as defined by the team) to match grading and support expectations.

### Invoice delivery & exports (V2)

- **FR32:** An authenticated user can **send** an **invoice by email** to one or more **recipient addresses**, with a **text message** (subject/body as defined by the team) and the **generated PDF** for that invoice attached.
- **FR33:** Email send uses the **same invoice data** as **FR24–FR27** at send time; on failure, the user receives a **clear error** and the system does not claim success.
- **FR34:** An authenticated user can **export** their **clients** to a **CSV** file with a **documented column set**.
- **FR35:** An authenticated user can **export** their **invoices** to a **CSV** file with a **documented column set** (including amounts and identifiers consistent with stored data).

### Dashboard & analytics (V2)

- **FR36:** An authenticated user can view a **dashboard** showing **total revenue** across their invoices per **documented revenue rules** (e.g. based on status such as **paid**—team-defined and consistent with exports).
- **FR37:** The dashboard shows the **total count of invoices** for the account.
- **FR38:** The dashboard shows **revenue aggregated by client**.
- **FR39:** The dashboard shows **revenue aggregated by month** (calendar month or billing period—team-defined).
- **FR40:** All dashboard figures in **FR36–FR39** are **computed from persisted** invoice (and related) data, not static placeholders.

### PDF branding (V2)

- **FR41:** An authenticated user can **upload** or **replace** a **logo image** used for invoice PDFs (format/size limits as defined by the team).
- **FR42:** **Generated PDFs** can render the user’s **current logo** without obscuring **mandatory invoice content** required by the implemented rules.

### Caching (V2)

- **FR43:** The product can apply a **documented caching strategy** to reduce redundant computation or database reads for **dashboard** and **list** endpoints while preserving **correctness** after **create/update/delete** operations affecting those views (**invalidation or TTL** documented per endpoint class).

## Non-Functional Requirements

### Performance

- **NFR-P1:** Primary interactive flows (sign-in, list/detail views, save invoice, trigger PDF) complete within a **team-defined** response-time budget acceptable for a web SaaS (exact thresholds recorded with the **load-test** report).
- **NFR-P2:** A **load test** runs against the **staging** environment before promotion to production; **metrics** (e.g. throughput, latency percentiles, error rate) are **documented** and meet **team-agreed** thresholds for the V1 release.
- **NFR-P3:** PDF generation may be slower than simple CRUD; the user receives **feedback** (e.g. in-progress state) so the UI does not appear frozen during generation.

### Security

- **NFR-S1:** All browser-to-server traffic uses **HTTPS** in staging and production.
- **NFR-S2:** Credentials and session tokens are handled using **industry-standard** practices (e.g. salted hashing for passwords, secure session or token settings); secrets are **not** committed to the repository.
- **NFR-S3:** **Authorization** is enforced server-side for every mutating and read operation on domain data (clients, services, invoices, PDFs)—**FR4** is technically guaranteed, not UI-only.
- **NFR-S4:** **Personal data** (freelancer and client PII) is processed in line with **GDPR** expectations: **minimal** collection, **restricted** access, and a **documented** stance on retention and deletion in README or privacy notice as appropriate for V1.

### Scalability

- **NFR-SC1:** V1 targets a **small concurrent user base** (course and early adopters); the architecture may assume **modest** load, but **staging** load tests must still demonstrate **predictable** behavior under the agreed scenario.
- **NFR-SC2:** If traffic grows, the team can **scale** the deployment path (e.g. more instances, managed DB) **without** redesigning core domain rules—exact mechanism is implementation-specific.

### Accessibility

- **NFR-A1:** The web UI follows a **reasonable baseline** for keyboard and screen-reader use on primary flows (navigation, forms, actions); the team may target **WCAG 2.1 Level A** as a stretch goal where feasible for V1.

### Integration

- **NFR-I1:** **No** mandatory integration with external billing, tax, or accounting systems in V1; outbound **PDF** remains the primary artifact. Future integrations are **out of scope** for NFR validation unless added to the PRD.
- **NFR-I2 (V2):** **Transactional email** integration (or equivalent) is **required** for **FR32–FR33**; provider credentials are **environment-managed**; rate limits and failures are **handled** without exposing secrets to clients. **Transient failures:** at least **one bounded automatic retry** before surfacing a user-visible failure (total wait and retry count **documented** in runbook); **permanent** failures (bad address, policy rejection) return a **clear, non-success** outcome without implying delivery.

### Reliability, caching & data freshness (V2)

- **NFR-C1:** Cached responses for **dashboard** and **lists** reflect **eventual consistency** bounded by a **team-documented** maximum staleness after writes, or use **invalidation** that guarantees **read-your-writes** for the acting user where feasible.
- **NFR-C2:** Cache mechanism (e.g. **process memory**, **shared external cache**, or **HTTP caching semantics**) is **implementation-specific** but must be **safe for multi-instance** deployment if the team runs **more than one** app instance in staging/production.

### Observability & monitoring (V2)

- **NFR-O1:** Application logs for **API and background tasks** use **machine-parseable fields** (severity level, timestamp, correlation identifier per request where applicable) consistent across services, and contain **no unredacted secrets**.
- **NFR-O2:** A **monitoring dashboard or page** is reachable in **staging** and **production** via **embedded health UI**, **vendor-hosted operations console**, or **provider-native metrics** linked from README—without mandating a specific vendor—and exposes at minimum **service liveness**, **recent errors** or error rate, and **optional** additional metrics agreed by the team.
- **NFR-O3:** **CI/CD** pipelines and **deployment** practices updated for V2 remain **green** on the default branch for the same classes of checks as V1 unless the team **documents** intentional scope changes.

### V2 delivery & governance

- **NFR-G1:** V2 ships with **clean backlog hygiene** (issues/PRs traceable to requirements **FR32+**), a **tagged release** (recommended **v2.0.0** or team scheme), and **release notes** describing operational changes (email provider, cache, monitoring).
- **NFR-G2:** The **production** and **staging** environments remain **reachable** after V2 rollout; rollback or feature-flag strategy is **documented** if email or cache introduces new failure modes.

## Measurement & operational definitions

This section **closes measurability gaps** called out in validation: it binds **FR36–FR40**, **FR43**, **NFR-P1–P2**, **NFR-C1**, **NFR-I2**, and **NFR-O1–O2** once the team **records** overrides in the **load-test report**, **README**, or **runbook**. Until overridden, the **defaults** below apply for **release acceptance**.

| Topic | Rule | Default (if not superseded in writing) |
|-------|------|------------------------------------------|
| **Revenue in dashboard & exports** | Which invoices and which amounts roll into “revenue.” | **Status = paid** only; amount = **TTC** in **EUR**; attribution date = **`paidAt`** if present, else **`updatedAt`** (document which field ships). |
| **Revenue by month** | Month boundary for aggregation. | **Calendar month**, timezone **Europe/Paris**, unless the team documents **UTC** instead (same choice for UI and CSV). |
| **CSV exports** | Encoding and contract stability. | **UTF-8**; header row **versioned** (e.g. `export-schema=v1` in README or API doc). |
| **Interactive API p95** | Latency under agreed load-test scenario on **staging**. | **Illustrative course target:** p95 **< 500 ms** for authenticated **non-PDF** list/detail paths under the reference scenario—replace with team numbers when available. |
| **Cache freshness** | Balance staleness vs load (see **NFR-C1**). | **Read-your-writes** for the **acting user** on dashboard and own invoice/client lists; for other readers, **≤ 60 s** eventual staleness unless **invalidation** fires first. |
| **Email transient failures** | Retry and user-visible outcome (see **NFR-I2**). | **One** automatic retry for provider-classified transient errors; **cumulative wait cap** documented in runbook (e.g. ≤ 30 s) unless team changes it. |
| **Structured logs** | Minimum fields for searchability (see **NFR-O1**). | **timestamp**, **severity**, **service name**, **correlation id** on HTTP-served requests; payload shapes **versioned** in runbook. |
