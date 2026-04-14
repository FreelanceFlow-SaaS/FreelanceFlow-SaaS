# Story 1.6: Frontend - Authenticated shell, navigation, profile page, and sign out

Status: review

## Story

En tant qu'utilisateur authentifie,  
Je veux un shell avec sidebar persistante, acces profil et deconnexion,  
Afin de naviguer clairement vers les flux de facturation.

## Acceptance Criteria

1. **Given** un utilisateur connecte  
   **When** il arrive dans l'app  
   **Then** le shell affiche une sidebar gauche avec Factures, Clients, Prestations, Profil vendeur, un etat actif, et les landmarks `main`/`nav`.
2. **Given** la page profil vendeur  
   **When** l'utilisateur consulte ou met a jour son profil  
   **Then** les appels utilisent l'API profil de la Story 1.4 avec feedback de validation.
3. **Given** le ThemeSwitcher  
   **When** l'utilisateur change de theme  
   **Then** l'etat des formulaires en cours n'est pas reset.
4. **Given** l'action deconnexion  
   **When** l'utilisateur se deconnecte  
   **Then** la session est terminee et les routes protegees deviennent inaccessibles sans reconnexion.

## Tasks / Subtasks

- [x] Construire le layout authentifie
  - [x] Implementer `src/app/(app)/layout.tsx` avec sidebar persistante
  - [x] Ajouter navigation vers Factures, Clients, Prestations, Profil vendeur
  - [x] Gerer etat actif de route et accessibilite (`nav`, `main`, tailles de cibles)
- [x] Integrer le profil vendeur
  - [x] Ajouter page `src/app/(app)/profil-vendeur/page.tsx`
  - [x] Connecter `GET/PATCH` profil et mapping des erreurs FR
- [x] Integrer la deconnexion
  - [x] Ajouter action sign out dans shell (menu compte ou bouton)
  - [x] Nettoyer jetons/session et rediriger vers login
- [x] Stabiliser ThemeSwitcher
  - [x] Verifier absence de reset du state formulaire sur changement de theme
- [x] Tester les parcours critiques
  - [x] Navigation et protection de routes
  - [x] Profil vendeur et deconnexion

## Dev Notes

- Les routes pas encore implementees peuvent etre placeholders, mais la navigation doit etre coherente et stable.
- Le shell doit rester robuste sur desktop et tablette (sidebar pleine, rail icone ou drawer selon choix equipe).
- Toute erreur API profil utilise le format unifie Nest vers messages FR.

### Project Structure Notes

- `src/app/(app)/layout.tsx`
- `src/app/(app)/profil-vendeur/page.tsx`
- `src/components/layout/app-shell.tsx` (sidebar + ThemeSwitcher header + deconnexion)
- `src/middleware.ts` (protection cookie `ff_access_token`)
- `src/lib/api/profile-api.ts`
- `src/lib/auth/*`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 1.6, FR3, FR5, UX-DR4/10/17)
- `_bmad-output/planning-artifacts/architecture.md` (Structure App Router, modules auth/frontend)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (Direction 1, sidebar, ThemeSwitcher, accessibilite)

## Dev Agent Record

### Agent Model Used

Cursor agent (implementation Epic 1)

### Debug Log References

- N/A

### Completion Notes List

- Shell: `AppShell` avec `nav` (`aria-label`), liens actifs, `main#main`, ThemeSwitcher dans le header, bouton deconnexion appelant `POST /auth/logout` + `clearAccessToken`.
- Profil: `GET/PATCH /users/profile` (Bearer + `credentials`), messages FR via `mapApiErrorToMessage`.
- Middleware: redirection `/login` si absence du cookie `ff_access_token` sur `/factures`, `/clients`, `/prestations`, `/profil-vendeur`.
- Tests: `app-shell.test.tsx`, `theme-form-stability.test.tsx`.
- Commits: `feat(#2):` x3 (shell+nav, profil API, tests).

### File List

- `frontend/src/middleware.ts`
- `frontend/src/components/layout/app-shell.tsx`
- `frontend/src/components/layout/app-shell.test.tsx`
- `frontend/src/components/theme-form-stability.test.tsx`
- `frontend/src/components/profile/profile-form.tsx`
- `frontend/src/app/(app)/layout.tsx`
- `frontend/src/app/(app)/factures/page.tsx`
- `frontend/src/app/(app)/clients/page.tsx`
- `frontend/src/app/(app)/prestations/page.tsx`
- `frontend/src/app/(app)/profil-vendeur/page.tsx`
- `frontend/src/lib/api/profile-api.ts`
- `frontend/src/components/theme-provider.tsx` (ajustements hydration / lint)
- `_bmad-output/implementation-artifacts/1-6-frontend-authenticated-shell-profile-signout.md`

## Change Log

- 2026-04-11: Implementation story 1.6 dans le repo `frontend` (meme branche epic 1).
