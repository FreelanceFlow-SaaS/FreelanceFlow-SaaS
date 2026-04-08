# Story 6.1: Dockerfiles and local full stack

Status: in-progress

<!-- Ultimate context engine analysis completed - comprehensive developer guide created -->

## Story

As a developer,  
I want Dockerfiles and compose (or a documented equivalent) for the frontend, backend, and database,  
so that anyone can run the full stack locally with reproducible steps.

## Acceptance Criteria

1. **Given** each deployable application (Next.js frontend, NestJS API)  
   **When** `docker build` runs from the documented context  
   **Then** images build successfully and the runtime command starts the correct production server (not a broken partial copy of sources).

2. **Given** local orchestration (`docker compose` or equivalent)  
   **When** the developer runs the documented command (e.g. `docker compose up --build`)  
   **Then** PostgreSQL, API, and UI are reachable on documented ports with a shared Docker network.

3. **Given** a new contributor  
   **When** they follow the root (or per-repo) README  
   **Then** they see explicit ports, required environment variables, and the exact compose/build commands.

4. **Given** repository hygiene (NFR5)  
   **When** secrets are needed for local or container runs  
   **Then** only `.env.example` (or equivalent templates with placeholder values) is committed—no real credentials, JWT secrets, or database passwords in Git.

## Tasks / Subtasks

- [x] **Frontend production image (AC: 1, 3)**  
  - [x] Audit and fix `frontend/Dockerfile`: Next.js requires the build output (`.next/`), static assets (`public/` if present), and config files (`next.config.*`, Tailwind/PostCSS if required at build). Prefer [Next.js `output: 'standalone'`](https://nextjs.org/docs/app/building-your-application/deploying#docker-image) for a smaller runtime image, or copy `.next` + minimal `node_modules` explicitly—**do not** ship only `src/` without `.next`.  
  - [x] Add `.dockerignore` under `frontend/` to exclude `node_modules`, `.next`, `.git`, and local env files from build context.

- [ ] **Backend production image (AC: 1, 3)**  
  - [ ] Add `Dockerfile` in the NestJS project root (separate repo or `backend/` in monorepo—match where the API actually lives). Use multi-stage: `npm ci`, `npm run build`, runner stage with production `node_modules` and compiled `dist/`.  
  - [ ] Add `.dockerignore` for the API tree.

- [ ] **Compose full stack (AC: 2, 3)**  
  - [ ] Add `docker-compose.yml` (or `compose.yaml`) at the chosen root: workspace root if monorepo, or document a third “infra” folder per [Source: `_bmad-output/planning-artifacts/architecture.md` — Infrastructure and Deployment].  
  - [ ] Services: `db` (official `postgres` image with named volume), `api` (depends_on `db`, healthcheck or retry for migrations), `web` (depends_on `api` or documented startup order).  
  - [ ] Wire env: `DATABASE_URL` (or discrete `POSTGRES_*` + Nest config), `JWT`/`REFRESH` secrets as **build args only if unavoidable**—prefer runtime env from compose `environment:` + local `.env` gitignored.

- [ ] **Documentation and templates (AC: 3, 4)**  
  - [ ] README section: prerequisites (Docker Desktop or Engine + Compose v2), one-liner up, table of ports (e.g. UI `3000`, API `3001` or `4000`, Postgres `5432`—use actual chosen values).  
  - [ ] Commit `.env.example` at compose scope (root or per service) listing every variable with safe placeholders; document copying to `.env`.  
  - [ ] If CORS or public API URL is required for the browser, document `NEXT_PUBLIC_*` vs server-only vars.

- [ ] **Verification (AC: 1–4)**  
  - [ ] From a clean machine simulation: `docker compose build` and `docker compose up` without relying on host `node_modules`.  
  - [ ] Confirm `git status` never tracks `.env` (add to `.gitignore` if missing).

## Dev Notes

### Epic and cross-story context

- **Epic 6** delivers quality, deployment, and traceability (FR30, FR31 at epic level; this story focuses on runnable containers and local parity).  
- **Story 6.2** will add CI/CD; **6.3** load tests on staging; **6.4** README/OpenAPI/version/legal copy; **6.5** HTTPS and a11y verification. Keep compose and Dockerfiles **simple and CI-friendly** so 6.2 can reuse the same Dockerfiles in pipelines.

### Architecture compliance

- **Two-repo model (canonical):** Dockerfile in **each** Git repository; compose may live in a third infra repo or be documented to run both images on a shared network [Source: `_bmad-output/planning-artifacts/architecture.md` — Infrastructure and Deployment].  
- **Monorepo reality (this workspace):** If frontend and backend folders share one clone, place compose at the **workspace root** with `build.context` pointing to `frontend/` and `backend/` (or future API path). Document the mapping so split-repo teams can mirror the same compose logic.  
- **Sequence:** Architecture lists “Wire Docker and compose” before CI/CD [Source: `_bmad-output/planning-artifacts/architecture.md` — Decision Impact and Implementation Sequence, step 5].

### Technical requirements

- **PostgreSQL** is the mandated database; container must match the version the team uses locally (pin image tag, e.g. `postgres:16-alpine`).  
- **Nest** must run migrations or document a `command:` that runs migrate then start (team choice; document clearly).  
- **Next.js 16.x** is pinned in `frontend/package.json`; Docker Node image should match the project’s supported Node line (currently `node:22-alpine` in existing Dockerfile—keep aligned with team Node policy).  
- **Security (NFR5):** salted password hashing is application-level; for Docker, NFR5 here means **no secrets in Git**—use `.env.example` only for committed templates [Source: `_bmad-output/planning-artifacts/epics.md` — NFR5, Story 6.1 AC].

### File structure (expected touchpoints)

| Area | Path (adjust if backend folder name differs) |
|------|-----------------------------------------------|
| Frontend image | `frontend/Dockerfile`, `frontend/.dockerignore` |
| Backend image | `backend/Dockerfile` (or repo root of Nest app), `backend/.dockerignore` |
| Orchestration | `docker-compose.yml` at workspace root (or `infra/docker-compose.yml` + README link) |
| Env template | `.env.example` (root or next to compose) |
| Docs | `README.md` (root or primary entrypoint reviewers use) |

### Testing requirements

- Automated tests in CI are **Story 6.2**; for **6.1**, minimum bar is **manual or scripted smoke**:  
  - `docker compose config` succeeds (valid YAML and interpolation).  
  - `docker compose build` completes for all services.  
  - After `up`, HTTP health check: API `/api/v1` or health route if present; UI root returns 200.  
- Optional: add a short `docs/local-docker.md` only if README would become unwieldy (keep duplication low).

### Previous story intelligence (Epic 5 → Epic 6)

- **Story 5.2** established PDF UX patterns under `frontend/src/app/` and feature components; no direct conflict with Docker work. Preserve **existing** `frontend` scripts (`build` / `start`) when validating the container—do not change package scripts unless required for production in Docker.

### Brownfield: existing `frontend/Dockerfile`

- Current file runs `npm run build` then **omits** `.next` in the runner stage—**production start will fail or serve nothing useful**. Treat fixing this as **in scope** for AC1.

### Latest technical notes (March 2026)

- Docker Compose V2 is invoked as `docker compose` (space); document accordingly.  
- For Next.js 16, prefer **standalone** output in `next.config.ts`/`js` for Docker per official deployment docs to reduce image size and copy errors.

### Project context reference

- No `project-context.md` found in workspace; follow architecture + epics as SOt for paths and stack.

## Dev Agent Record

### Agent Model Used

Composer (Cursor agent)

### Debug Log References

### Completion Notes List

- **Frontend scope only (Story 6.1 partial):** Enabled `output: "standalone"` in `next.config.ts`. Replaced broken runner stage (previously copied `src/` without `.next`) with multi-stage build: builder runs `npm ci` + `next build`; runner copies `.next/standalone`, `.next/static`, and `public/`, runs `node server.js` as non-root `nextjs`. Added `frontend/.dockerignore` for context hygiene. Set `HUSKY=0` in builder to skip husky when `.git` is absent. Verified: `docker build` succeeds; container returns HTTP 200 on `/`. Full-stack compose, backend image, README, and `.env.example` remain for the rest of 6.1.

### File List

- `frontend/next.config.ts`
- `frontend/Dockerfile`
- `frontend/.dockerignore`

### Change Log

- 2026-03-27: Story 6.1 — Frontend production Docker image (standalone) and `.dockerignore`; story status `in-progress` until backend/compose/docs tasks complete.
