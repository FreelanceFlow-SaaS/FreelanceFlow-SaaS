# Story 1.5: Frontend - Next.js bootstrap, design system tokens, and auth screens

Status: review

## Story

En tant que visiteur ou utilisateur,  
Je veux des ecrans d'inscription et de connexion en francais avec des formulaires accessibles,  
Afin d'utiliser l'application sans confusion ni blocage.

## Acceptance Criteria

1. **Given** une app Next.js App Router (`src/app`) avec Tailwind et primitives Radix encapsulees dans `src/components/ui/`  
   **When** l'utilisateur ouvre les pages inscription/connexion  
   **Then** la copie est en francais, les labels sont visibles, les focus rings utilisent le token `ring`, et les themes (`default`, `ocean`, `forest`, `high-contrast`) sont pilotables via `data-theme` avec persistance `localStorage`.
2. **Given** le switch theme/appearance est disponible  
   **When** l'utilisateur change le theme ou l'apparence  
   **Then** `data-appearance` (optionnel) et `prefers-reduced-motion` sont respectes.
3. **Given** une erreur API NestJS  
   **When** la soumission echoue  
   **Then** l'erreur est mappee vers un message francais court et les champs valides gardent leur valeur.

## Tasks / Subtasks

- [x] Initialiser le socle UI auth
  - [x] Verifier l'architecture Next App Router et les routes `src/app/(auth)/login` et `src/app/(auth)/register`
  - [x] Creer les composants UI reutilisables dans `src/components/ui/` (Input, Label, Button, Alert)
- [x] Mettre en place les tokens et themes
  - [x] Definir les tokens semantiques CSS variables pour `default`, `ocean`, `forest`, `high-contrast`
  - [x] Implementer le switch via `data-theme` + persistance `localStorage`
  - [x] Ajouter le support optionnel `data-appearance` et fallback `prefers-reduced-motion`
- [x] Implementer les formulaires auth accessibles
  - [x] Labels explicites, `aria-invalid`, `aria-describedby` et etat disabled/loading
  - [x] Mapping des erreurs backend (`code`, `details`) vers messages FR actionnables
  - [x] Preservation des champs non invalides apres echec
- [x] Couvrir les tests critiques
  - [x] Tests unitaires/composants pour rendu FR, etats erreur, et focus
  - [x] Test d'integration pour persistance de theme

## Dev Notes

- Respecter strictement le pattern "Radix primitifs encapsules dans `src/components/ui/`". Ne pas utiliser les primitifs directement dans les pages.
- Rester sur une seule strategie de communication API (direct Nest avec CORS ou Route Handlers Next en BFF) et la documenter dans le README.
- Ne pas hardcoder les couleurs: utiliser uniquement les tokens CSS variables.
- Les messages d'erreur doivent etre courts, en francais, et orientes action utilisateur.

### Project Structure Notes

- `src/app/(auth)/login/page.tsx`
- `src/app/(auth)/register/page.tsx`
- `src/components/ui/*`
- `src/lib/theme/*`
- `src/lib/auth/*`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 1.5, UX-DR1/2/3/10/12/14/18/19/20, FR29)
- `_bmad-output/planning-artifacts/architecture.md` (Next.js App Router, `src/app`, `src/components/ui/`, patterns frontend)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (Design system foundation, theming, a11y formulaires)

## Dev Agent Record

### Agent Model Used

Cursor agent (implementation Epic 1)

### Debug Log References

- N/A

### Completion Notes List

- UI auth: Radix Label + Slot (Button), tokens CSS par `data-theme` / `data-color-mode`, ThemeProvider + ThemeSwitcher, formulaires login/register en FR avec `fetch` direct Nest (`NEXT_PUBLIC_API_URL`), `credentials: include`, JWT + cookie miroir pour middleware.
- README: strategie API documentee ; `.env.example` pour la base URL.
- Commits frontend (branche `feature/epic-1-account-session-seller-profile`): `feat(#1):` x4 (foundation, themes, forms, tests).

### File List

- `frontend/package.json`
- `frontend/package-lock.json`
- `frontend/.env.example`
- `frontend/README.md`
- `frontend/vitest.config.ts`
- `frontend/src/test/setup.ts`
- `frontend/src/app/globals.css`
- `frontend/src/app/layout.tsx`
- `frontend/src/app/page.tsx`
- `frontend/src/app/(auth)/layout.tsx`
- `frontend/src/app/(auth)/login/page.tsx`
- `frontend/src/app/(auth)/register/page.tsx`
- `frontend/src/components/ui/alert.tsx`
- `frontend/src/components/ui/button.tsx`
- `frontend/src/components/ui/input.tsx`
- `frontend/src/components/ui/label.tsx`
- `frontend/src/components/theme-provider.tsx`
- `frontend/src/components/theme-switcher.tsx`
- `frontend/src/components/auth/login-form.tsx`
- `frontend/src/components/auth/register-form.tsx`
- `frontend/src/lib/utils.ts`
- `frontend/src/lib/theme/constants.ts`
- `frontend/src/lib/api/base-url.ts`
- `frontend/src/lib/auth/constants.ts`
- `frontend/src/lib/auth/session.ts`
- `frontend/src/lib/auth/map-api-error.ts`
- `frontend/src/lib/auth/auth-api.ts`
- `frontend/src/lib/auth/map-api-error.test.ts`
- `frontend/src/components/auth/login-form.test.tsx`
- `frontend/src/components/theme-switcher.test.tsx`
- `_bmad-output/implementation-artifacts/1-5-frontend-auth-screens-and-theming.md`

## Change Log

- 2026-04-11: Implementation story 1.5 dans le repo `frontend` (PR a ouvrir vers `develop`).
