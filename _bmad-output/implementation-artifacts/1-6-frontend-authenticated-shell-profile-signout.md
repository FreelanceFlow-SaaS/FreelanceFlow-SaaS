# Story 1.6: Frontend - Authenticated shell, navigation, profile page, and sign out

Status: ready-for-dev

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

- [ ] Construire le layout authentifie
  - [ ] Implementer `src/app/(app)/layout.tsx` avec sidebar persistante
  - [ ] Ajouter navigation vers Factures, Clients, Prestations, Profil vendeur
  - [ ] Gerer etat actif de route et accessibilite (`nav`, `main`, tailles de cibles)
- [ ] Integrer le profil vendeur
  - [ ] Ajouter page `src/app/(app)/profil-vendeur/page.tsx`
  - [ ] Connecter `GET/PATCH` profil et mapping des erreurs FR
- [ ] Integrer la deconnexion
  - [ ] Ajouter action sign out dans shell (menu compte ou bouton)
  - [ ] Nettoyer jetons/session et rediriger vers login
- [ ] Stabiliser ThemeSwitcher
  - [ ] Verifier absence de reset du state formulaire sur changement de theme
- [ ] Tester les parcours critiques
  - [ ] Navigation et protection de routes
  - [ ] Profil vendeur et deconnexion

## Dev Notes

- Les routes pas encore implementees peuvent etre placeholders, mais la navigation doit etre coherente et stable.
- Le shell doit rester robuste sur desktop et tablette (sidebar pleine, rail icone ou drawer selon choix equipe).
- Toute erreur API profil utilise le format unifie Nest vers messages FR.

### Project Structure Notes

- `src/app/(app)/layout.tsx`
- `src/app/(app)/profil-vendeur/page.tsx`
- `src/components/navigation/*`
- `src/components/theme-switcher/*`
- `src/lib/auth/*`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 1.6, FR3, FR5, UX-DR4/10/17)
- `_bmad-output/planning-artifacts/architecture.md` (Structure App Router, modules auth/frontend)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (Direction 1, sidebar, ThemeSwitcher, accessibilite)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour shell authentifie, navigation et profil.

### File List

- `_bmad-output/implementation-artifacts/1-6-frontend-authenticated-shell-profile-signout.md`
