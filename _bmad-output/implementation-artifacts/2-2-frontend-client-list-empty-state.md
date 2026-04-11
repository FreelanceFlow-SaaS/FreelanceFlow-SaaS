# Story 2.2: Frontend - Client list and empty state

Status: review

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

- [x] Implementer la page liste clients
  - [x] Route `src/app/(app)/clients/page.tsx`
  - [x] Requete API liste clients et etats loading/error/success
- [x] Implementer `ResourceEmptyState` pour clients
  - [x] Copy FR concise + CTA principal "Ajouter un client"
- [x] Implementer tableau/liste clients
  - [x] Colonnes minimales (nom, email, entreprise)
  - [x] Lien vers route detail/edition
- [x] Couvrir accessibilite et tests
  - [x] Focus clavier, intitulés explicites, ordre des titres
  - [x] Test des 2 etats: vide vs non vide

## Dev Notes

- Reutiliser `ResourceEmptyState` pour homogeniser les pages listes (clients, prestations, factures).
- Prioriser la lisibilite FR et la rapidite de comprehension (1 titre, 1 phrase, 1 CTA).

### Project Structure Notes

- `src/app/(app)/clients/page.tsx`
- `src/components/shared/resource-empty-state.tsx`
- `src/modules/clients/components/clients-list.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 2.2, UX-DR9/15/16/20)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (`ResourceEmptyState`, accessibilite, copy FR)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 2)

### Debug Log References

- N/A

### Completion Notes List

- Liste `GET /api/v1/clients`, skeleton de chargement, tableau avec `caption` sr-only, liens vers `/clients/[id]/edit`.
- Commit frontend: `feat(#3): Add client list page and empty state`.

### File List

- `frontend/src/lib/api/clients-api.ts`
- `frontend/src/components/shared/resource-empty-state.tsx`
- `frontend/src/components/shared/resource-empty-state.test.tsx`
- `frontend/src/modules/clients/components/clients-list.tsx`
- `frontend/src/modules/clients/components/clients-list.test.tsx`
- `frontend/src/app/(app)/clients/page.tsx`
- `_bmad-output/implementation-artifacts/2-2-frontend-client-list-empty-state.md`

## Change Log

- 2026-04-11: Implementation story 2.2 (branche `feature/epic-2-client-directory`).
