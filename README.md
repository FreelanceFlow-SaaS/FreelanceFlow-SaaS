# FreelanceFlow — SaaS (monorepo)

## Git branching and deployments

Promotion path (team convention):

`feature/*` → **`develop`** → **`staging`** → **`main`** (production)

| Git ref | Role |
| --- | --- |
| `feature/*` | Short-lived branches; land via PR (typically into `develop`). |
| `develop` | Integration; CI runs on PRs and pushes. |
| `staging` | Pre-production; push triggers **staging** GitHub Environment and frontend image publish (see CI). |
| `main` | Production; push triggers **production** environment and image publish. |

Configure **GitHub Environments** named `staging` and `production` in the repository settings (protection rules on `production` as needed). The frontend workflow assigns the job to the environment based on the branch.

## Frontend CI/CD

Workflow: [`.github/workflows/frontend-ci.yml`](.github/workflows/frontend-ci.yml).

On PRs and pushes targeting `develop`, `staging`, or `main` (with `frontend/**` path filters), the pipeline runs:

1. `npm ci` (with `HUSKY=0` in Actions)
2. `npm run lint`
3. `npm run format:check`
4. `npm test`
5. `npm run build`
6. `docker build` using [`frontend/Dockerfile`](frontend/Dockerfile) with context `frontend/` (same as Story 6.1 / local builds)

On push to `staging` or `main` (non-fork), the image is pushed to **GHCR** as:

`ghcr.io/<owner>/<repo>-frontend:<sha>` and `:latest` (name lowercased in the workflow).

### Secrets and configuration

- **No secrets are committed** (NFR5). Use GitHub **Actions secrets** and **Environments** only.
- **GHCR push** uses `GITHUB_TOKEN` with `packages: write` (no extra secret for the same repository).
- **Deploy to a host** (Fly.io, Railway, Render, etc.) is not wired in this repo yet: add a deploy job and the provider’s secrets when infrastructure is ready; do not treat a no-op step as a real deployment.

### Split frontend into its own repository later

Copy `.github/workflows/frontend-ci.yml` to the new repo root, remove `frontend/` path prefixes and `working-directory` / `paths` filters as needed, and keep the same `Dockerfile` at the app root.

## Local parity (Docker)

See Story 6.1: [`_bmad-output/implementation-artifacts/6-1-dockerfiles-local-full-stack.md`](_bmad-output/implementation-artifacts/6-1-dockerfiles-local-full-stack.md).
