# Story 2.2: Frontend - Client list and empty state

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux voir ma liste de clients ou un etat vide utile,  
Afin de savoir comment creer mon premier client.

## Acceptance Criteria

1. **Given** l'ouverture de la section Clients  
   **When** aucun client n'existe  
   **Then** `ResourceEmptyState` affiche un titre FR, une phrase, et un CTA principal de creation.
2. **Given** des clients existants  
   **When** la liste charge  
   **Then** un skeleton/spinner est visible pendant le fetch et chaque ligne permet la navigation vers detail/edition.

## Tasks / Subtasks

- [ ] Implementer la page liste clients
  - [ ] Route `src/app/(app)/clients/page.tsx`
  - [ ] Requete API liste clients et etats loading/error/success
- [ ] Implementer `ResourceEmptyState` pour clients
  - [ ] Copy FR concise + CTA principal "Ajouter un client"
- [ ] Implementer tableau/liste clients
  - [ ] Colonnes minimales (nom, email, entreprise)
  - [ ] Lien vers route detail/edition
- [ ] Couvrir accessibilite et tests
  - [ ] Focus clavier, intitulés explicites, ordre des titres
  - [ ] Test des 2 etats: vide vs non vide

## Dev Notes

- Reutiliser `ResourceEmptyState` pour homogeniser les pages listes (clients, prestations, factures).
- Prioriser la lisibilite FR et la rapidite de comprehension (1 titre, 1 phrase, 1 CTA).

### Project Structure Notes

- `src/app/(app)/clients/page.tsx`
- `src/components/shared/resource-empty-state.tsx`
- `src/modules/clients/*`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 2.2, UX-DR9/15/16/20)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (`ResourceEmptyState`, accessibilite, copy FR)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour la liste clients et l'etat vide.

### File List

- `_bmad-output/implementation-artifacts/2-2-frontend-client-list-empty-state.md`
