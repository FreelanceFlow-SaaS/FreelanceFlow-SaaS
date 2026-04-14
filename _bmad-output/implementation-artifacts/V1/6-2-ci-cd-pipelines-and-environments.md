# Story 6.2: CI/CD pipelines and environments

Status: in-progress

<!-- Ultimate context engine analysis completed - comprehensive developer guide created -->

## Story

As a team member,  
I want GitHub Actions (or an equivalent CI system) in **each** repository to install dependencies, enforce quality gates, build artifacts, build Docker images, publish them, and deploy to **staging** and **production**,  
so that staging and production stay predictable and aligned with the agreed Git workflow.

## Acceptance Criteria

1. **Given** a repository (Next.js frontend and, when present, NestJS backend)  
   **When** a workflow runs on **relevant branches** (at minimum: PRs to `develop` / `main`, and pushes to `develop`, `staging`, and `main`—team may add `feature/*` via PR-only triggers)  
   **Then** the pipeline executes, in order: **dependency install** → **lint** → **Prettier format check** → **tests** (see Dev Notes for frontend vs backend bar) → **application build** → **Docker image build** → **registry publish** (when credentials are configured) → **deploy** to the environment mapped to that branch or promotion step.

2. **Given** the canonical two-repo architecture  
   **When** both repos exist  
   **Then** each repo has its own workflow file(s) under `.github/workflows/` (architecture names `ci.yml`; you may split `ci.yml` + `deploy.yml` if clearer) and **no secrets or registry tokens** are committed (NFR5)—only GitHub **Actions secrets** and **environments**.

3. **Given** the PRD technical success criteria  
   **When** a reviewer reads the repository docs  
   **Then** the **branch strategy** `feature/*` → `develop` → **`staging`** → **`main`** is documented (README or `docs/` link), including which branch triggers **staging** deploy vs **production** deploy and how promotion is expected to work (merge, tag, or manual workflow).

4. **Given** Story 6.1 deliverables  
   **When** Dockerfiles exist for a service  
   **Then** CI uses the **same** `Dockerfile` and build context as local/`docker compose` (no duplicate ad-hoc image definitions) so local and CI images stay aligned.

## Tasks / Subtasks

> **Scope note (2026-03-28):** Frontend-only slice executed; backend workflow, full-stack verification, and real deploy integration remain open.

- [ ] **Branch triggers and environments (AC: 1, 3)**  
  - [x] Define triggers: e.g. `pull_request` to `develop`/`main`/`staging`; `push` to `develop`, `staging`, `main`; optional `workflow_dispatch` for manual promotion.  
  - [ ] Map **GitHub Environments** (e.g. `staging`, `production`) with protection rules on `production` (required reviewers or restricted branches). _(Repository settings — README documents required names.)_  
  - [x] Document the mapping in root `README.md` (or linked doc): which git ref deploys where.

- [x] **Frontend workflow — quality + build (AC: 1, 2, 4)**  
  - [x] Path: `frontend/.github/workflows/` if frontend is a **separate clone**, or `.github/workflows/` at monorepo root with `defaults.run.working-directory: frontend` / `paths` filters.  
  - [x] Steps: checkout, setup Node (match `frontend/Dockerfile` / `package.json` engines if specified—today Node 22 is used in Docker), `npm ci`, `HUSKY=0` (or `npm ci --ignore-scripts` if team prefers) to avoid husky failures in CI, `npm run lint`, `npm run format:check`, `npm run build`.  
  - [x] **Tests:** Backend **must** run unit tests per PRD. For frontend, if `package.json` has no `test` script yet, add a minimal test setup (e.g. Vitest + one smoke test) **or** document in README that frontend test step is pending and track as follow-up—**do not** silently skip the test step without team-visible documentation. Preferred: add `npm test` and run it in CI.  
  - [x] Docker: `docker build` from the same context as Story 6.1 (`frontend/`). Tag with `${{ github.sha }}` and/or semver.

- [ ] **Backend workflow — quality + build (AC: 1, 2, 4)**  
  - [ ] Apply the same pattern when `backend/` (or Nest repo root) exists: `npm ci`, lint, format check, **`npm test`** (mandatory), `npm run build`, `docker build` using that service’s `Dockerfile`.  
  - [ ] If DB is required for tests, use **service container** (`postgres`) in the job or test with mocked repositories per existing Nest patterns—do not require production DB credentials.

- [ ] **Registry publish + deploy (AC: 1, 2)**  
  - [x] Authenticate to container registry (GHCR, Docker Hub, or course-provided registry) via `secrets.*`. _(Frontend: `GITHUB_TOKEN` + GHCR login in workflow.)_  
  - [x] Push images on successful mainline builds (e.g. push to `staging`/`main` or on tag `v*`). _(Frontend image on push to `staging`/`main`, non-fork.)_  
  - [ ] Deploy step: use the platform the team chose (Fly.io, Railway, Render, Kubernetes, etc.) via official action or CLI; parameterize **staging** vs **production** URLs and credentials via environments/secrets.  
  - [x] If hosting is not yet provisioned, implement workflow up to **build + scan** (optional) and use a **placeholder** deploy job gated behind an environment secret (document “flip when infra ready”)—**do not** fake success; document the gap in README. _(No fake deploy job; README states deploy not wired.)_

- [x] **Documentation (AC: 3)** _(frontend / monorepo CI scope)_  
  - [x] README section: required GitHub secrets (names only), environments, and branch flow.  
  - [x] Link to Story 6.1 for local parity (compose + env templates).

- [ ] **Verification (AC: 1–4)**  
  - [ ] Open a test PR and confirm all required checks appear and fail on intentional lint/format breakage.  
  - [ ] Confirm a green run produces an image (or documents why push is skipped on forks).

## Dev Notes

### Epic and cross-story context

- **Epic 6** covers quality, deployment, and traceability.  
- **Story 6.1** provides Dockerfiles and (when complete) compose; **6.2** must consume those Dockerfiles in CI.  
- **6.3** will run load tests **against staging**; **6.4** README/OpenAPI/version; **6.5** HTTPS and a11y checks. Pipelines should expose a **stable staging URL** early for 6.3.

### Architecture compliance

- **Separate pipelines per repo:** install → lint → format check → test → build → Docker build → publish → deploy to **staging** / **prod** [Source: `_bmad-output/planning-artifacts/architecture.md` — Infrastructure and Deployment].  
- **Implementation sequence:** “Wire Docker and compose; **then** CI/CD and staging promotion” [Source: `_bmad-output/planning-artifacts/architecture.md` — Decision Impact and Implementation Sequence, step 5].  
- **Expected locations:** `.github/workflows/ci.yml` under each repo root in the canonical layout [Source: `_bmad-output/planning-artifacts/architecture.md` — Project Structure and Boundaries].  
- **Quality:** lint, Prettier, pre-commit locally; **CI on every relevant pipeline run** [Source: `_bmad-output/planning-artifacts/architecture.md` — Executive Summary].

### PRD compliance

- **Technical Success:** CI runs install, lint, format check, tests, build, Docker image build, registry publish, automated deployment; Git workflow `feature/*` → `develop` → **`staging`** → **`main`** with staging used for pre-production validation [Source: `_bmad-output/planning-artifacts/prd.md` — Technical Success; User Journeys — Léa].

### Technical requirements

- **Secrets (NFR5):** never commit `.env`, registry passwords, or cloud API keys; use GitHub **Secrets** and **Environments** [Source: `_bmad-output/planning-artifacts/epics.md` — Story 6.1 AC, NFR5].  
- **Husky:** CI must not fail on `prepare`/husky when `.git` hooks are inappropriate—set `HUSKY=0` or use `npm ci --ignore-scripts` consistently with Story 6.1 frontend Docker notes.  
- **Node version:** align `actions/setup-node` with `frontend/Dockerfile` (currently `node:22-alpine`) unless `package.json` adds `engines` that override.  
- **Monorepo (current workspace):** only `frontend/` exists today; add workflows that target `frontend/` now, and add backend workflows when the Nest repo/folder lands—keep paths documented so a future split into two remotes is trivial (copy workflow + adjust root).  
- **Forks / dependabot:** use `if: github.event_name == 'push' && github.ref == 'refs/heads/...'` for deploy/push steps so external contributors do not need your secrets.

### File structure (expected touchpoints)

| Area | Path |
|------|------|
| Frontend CI | `frontend/.github/workflows/*.yml` **or** repo root `.github/workflows/` with `working-directory` / `paths` |
| Backend CI | `backend/.github/workflows/*.yml` (or equivalent when created) |
| Docs | Root `README.md` (branch + secrets + URLs) |

### Testing requirements

- **Backend:** unit tests **mandatory** in CI per PRD.  
- **Frontend:** run automated tests in CI once a `test` script exists; until then, document the interim and prefer adding a minimal test in the same story.  
- **Docker:** failing `docker build` must fail the workflow (no `continue-on-error` on build).

### Previous story intelligence (Story 6.1)

- Frontend **standalone** Next.js image: builder runs `npm ci` + `next build`; runner uses `.next/standalone`—CI Docker build must use the same `frontend/Dockerfile`.  
- `HUSKY=0` was used in Docker build to skip husky when `.git` is absent—mirror for `npm ci` in Actions.  
- Backend Dockerfile, compose, and root README tasks may still be **in progress** on 6.1; implement CI for backend when those paths exist, and avoid divergent Dockerfile copies.

### Latest technical notes (March 2026)

- Prefer **GitHub Actions** `actions/checkout@v4`, `actions/setup-node@v4` (or current v4 line), and **OIDC** to cloud deployers where supported to avoid long-lived cloud secrets.  
- **GHCR** (`ghcr.io/<owner>/<image>`) pairs cleanly with `GITHUB_TOKEN` for push within the same org/repo; cross-repo pulls may need PAT with read packages.

### Project context reference

- No `project-context.md` found in workspace; treat architecture + PRD + epics as source of truth.

## Dev Agent Record

### Agent Model Used

Composer (Cursor agent)

### Debug Log References

- Removed invalid duplicate `frontend/.github/workflows/frontend-ci.yml` (broken YAML); canonical workflow lives at repo root for monorepo layout.

### Completion Notes List

- **Frontend-only (2026-03-28):** Added root `.github/workflows/frontend-ci.yml` with path filters, Node 22, `HUSKY=0`, lint → Prettier check → Vitest → Next build → Docker build using `frontend/Dockerfile` + context `frontend/`. GHCR publish on push to `staging`/`main` (non-fork) with `staging`/`production` GitHub Environment assignment. Root `README.md` documents branch flow, secrets policy, and Story 6.1 link. Added `npm test`, `vitest.config.ts`, and `src/smoke.test.ts`. Backend workflow, GitHub Environment UI setup, deploy provider integration, and manual PR verification remain for full Story 6.2 closure.

### File List

- `.github/workflows/frontend-ci.yml`
- `README.md`
- `frontend/package.json`
- `frontend/vitest.config.ts`
- `frontend/src/smoke.test.ts`
- `_bmad-output/implementation-artifacts/6-2-ci-cd-pipelines-and-environments.md`

### Change Log

- 2026-03-27: Story 6.2 created — CI/CD pipelines, environments, branch mapping, registry and deploy guardrails.
- 2026-03-28: Frontend CI slice — GitHub Actions workflow, Vitest smoke test, root README; removed erroneous `frontend/.github` workflow file.
