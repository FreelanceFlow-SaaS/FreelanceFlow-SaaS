# Story 1.5: Frontend - Next.js bootstrap, design system tokens, and auth screens

Status: ready-for-dev

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

- [ ] Initialiser le socle UI auth
  - [ ] Verifier l'architecture Next App Router et les routes `src/app/(auth)/login` et `src/app/(auth)/register`
  - [ ] Creer les composants UI reutilisables dans `src/components/ui/` (Input, Label, Button, Alert)
- [ ] Mettre en place les tokens et themes
  - [ ] Definir les tokens semantiques CSS variables pour `default`, `ocean`, `forest`, `high-contrast`
  - [ ] Implementer le switch via `data-theme` + persistance `localStorage`
  - [ ] Ajouter le support optionnel `data-appearance` et fallback `prefers-reduced-motion`
- [ ] Implementer les formulaires auth accessibles
  - [ ] Labels explicites, `aria-invalid`, `aria-describedby` et etat disabled/loading
  - [ ] Mapping des erreurs backend (`code`, `details`) vers messages FR actionnables
  - [ ] Preservation des champs non invalides apres echec
- [ ] Couvrir les tests critiques
  - [ ] Tests unitaires/composants pour rendu FR, etats erreur, et focus
  - [ ] Test d'integration pour persistance de theme

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

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree avec exigences frontend, UX et architecture consolidees.

### File List

- `_bmad-output/implementation-artifacts/1-5-frontend-auth-screens-and-theming.md`
