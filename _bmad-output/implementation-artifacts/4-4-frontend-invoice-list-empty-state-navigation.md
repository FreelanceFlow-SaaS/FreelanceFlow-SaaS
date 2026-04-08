# Story 4.4: Frontend - Invoice list, empty state, and navigation

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux parcourir mes factures avec un tri basique,  
Afin de retrouver rapidement les documents.

## Acceptance Criteria

1. **Given** la section Factures  
   **When** aucune facture n'existe  
   **Then** `ResourceEmptyState` oriente vers la creation de facture.
2. **Given** des factures existantes  
   **When** la liste s'affiche  
   **Then** un tri minimal par date ou statut est disponible (si dans le scope) et chaque ligne ouvre la route detail.

## Tasks / Subtasks

- [ ] Implementer page liste factures
- [ ] Gerer etat vide via `ResourceEmptyState`
- [ ] Ajouter tri basique date/statut
- [ ] Ajouter deep links vers detail facture
- [ ] Tester navigation, tri et etat vide

## Dev Notes

- Garder le tri minimal V1, sans surcomplexifier (pas de filtre avance obligatoire).
- Assurer coherence des badges statut avec texte explicite (pas couleur seule).

### Project Structure Notes

- `src/app/(app)/factures/page.tsx`
- `src/modules/invoices/components/invoice-list.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.4, FR21, UX-DR9/16/21)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (listes, badges, empty state)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour liste factures et navigation detail.

### File List

- `_bmad-output/implementation-artifacts/4-4-frontend-invoice-list-empty-state-navigation.md`
