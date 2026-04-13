# FreelanceFlow — SaaS

Deux applications : API NestJS dans `backend/` et interface Next.js dans `frontend/`.

## Production

**URL Front:** `https://freelanceflow-frontend-one.vercel.app/`
**URL Swagger:** `https://freelanceflow-saas-backend.onrender.com/api/docs`

## Équipe

| Nom | Rôle / contribution |
| --- | --- |
| _Julien Vandamme_ | _Dev Frontend_ |
| _Mathis Delobel_ | _Dev Frontend_ |
| _Andre Kanmegne_ | _Dev Backend_ |
| _Djibril Camara_ | _DevOps_ |

## Sous-modules et stacks

### `backend/` — API

| Couche | Technologies |
| --- | --- |
| Runtime & langage | Node.js, **TypeScript** |
| Framework | **NestJS** 11 (Express) |
| Base de données | **PostgreSQL** via **Prisma** 5 (ORM, migrations) |
| Auth | **JWT**, **Passport** (`passport-jwt`), **bcryptjs** |
| API & validation | **Swagger** (OpenAPI), **class-validator** / **class-transformer** |
| Logs | **Pino** (`nestjs-pino`, `pino-http`) |
| Documents | **pdfmake** (factures PDF) |
| Qualité | **Jest**, **ESLint**, **Prettier**, **Husky** / **lint-staged** |
| Conteneur | **Docker** (voir artefacts d’implémentation Story 6.1) |

### `frontend/` — application web

| Couche | Technologies |
| --- | --- |
| Framework | **Next.js** 16 (App Router), **React** 19 |
| UI | **Tailwind CSS** 4, **Radix UI**, **class-variance-authority**, **clsx** / **tailwind-merge** |
| Tests | **Vitest**, **Testing Library** (React, user-event, jest-dom) |
| Qualité | **ESLint** (config Next), **Prettier**, **Husky** |
| Conteneur | **Docker** (`frontend/Dockerfile`, publication **GHCR** en CI) |

## Git : branches et déploiements

Parcours de promotion (convention d’équipe) :

`feature/*` → **`develop`** → **`staging`** → **`main`** (production)

| Référence Git | Rôle |
| --- | --- |
| `feature/*` | Branches à courte durée de vie ; intégration par PR (en général vers `develop`). |
| `develop` | Intégration ; la CI s’exécute sur les PR et les pushes. |
| `staging` | Préproduction ; un push déclenche l’**environnement** GitHub **staging** et la publication de l’image frontend (voir la CI). |
| `main` | Production ; un push déclenche l’environnement **production** et la publication des images. |

Configurer les **environnements GitHub** nommés `staging` et `production` dans les paramètres du dépôt (règles de protection sur `production` si besoin). Le workflow frontend affecte le job à l’environnement en fonction de la branche.

## Backend CI/CD

Workflow : [`.github/workflows/backend-ci.yml`](.github/workflows/backend-ci.yml).

Sur les PR et les pushes visant `develop`, `staging` ou `main` (avec filtres de chemins `backend/**`), le pipeline exécute :

1. `npm ci` (avec `HUSKY=0` dans Actions)
2. `npm run lint`
3. `npm run format:check`
4. `npm test`
5. `npm run build`
6. `docker build` avec [`backend/Dockerfile`](backend/Dockerfile) et le contexte `backend/` (même logique que la Story 6.1 / builds locaux)

Lors d’un push sur `staging` ou `main` (hors fork), l’image est poussée vers **GHCR** ainsi :

`ghcr.io/<owner>/<repo>-backend:<sha>` et `:latest` (nom passé en minuscules dans le workflow).

## Frontend CI/CD

Workflow : [`.github/workflows/frontend-ci.yml`](.github/workflows/frontend-ci.yml).

Sur les PR et les pushes visant `develop`, `staging` ou `main` (avec filtres de chemins `frontend/**`), le pipeline exécute :

1. `npm ci` (avec `HUSKY=0` dans Actions, répertoire de travail `frontend/`)
2. `npm run lint`
3. `npm run format:check`
4. `npm test`
5. `npm run build` (build Next.js)
6. `docker build` avec [`frontend/Dockerfile`](frontend/Dockerfile) et le contexte `frontend/` (même logique que la Story 6.1 / builds locaux)

Lors d’un push sur `staging` ou `main` (hors fork), l’image est poussée vers **GHCR** ainsi :

`ghcr.io/<owner>/<repo>-frontend:<sha>` et `:latest` (nom passé en minuscules dans le workflow).

### Secrets et configuration

- **Aucun secret n’est commité dans le dépôt.** Utiliser uniquement les **secrets Actions** et les **environnements** GitHub.
- Le push vers **GHCR** s’appuie sur `GITHUB_TOKEN` avec la permission `packages: write` (pas de secret supplémentaire pour le même dépôt).
- Le **déploiement vers un hébergeur** (Fly.io, Railway, Render, etc.) n’est pas encore branché dans ce dépôt : ajouter un job de déploiement et les secrets du fournisseur lorsque l’infra est prête ; ne pas confondre une étape vide avec un déploiement réel.
