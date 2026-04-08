# Story 3.2: Frontend - Service list and empty state

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux voir mes prestations ou un etat vide utile,  
Afin de preparer le catalogue avant facturation.

## Acceptance Criteria

1. **Given** la section Prestations  
   **When** le catalogue est vide  
   **Then** `ResourceEmptyState` guide vers la creation.
2. **Given** des prestations existantes  
   **When** la liste s'affiche  
   **Then** les taux horaires sont affiches en EUR avec chiffres tabulaires.

## Tasks / Subtasks

- [ ] Implementer la page liste prestations
- [ ] Reutiliser `ResourceEmptyState` pour etat vide
- [ ] Afficher les montants via `MoneyDisplay` en locale FR/EUR
- [ ] Appliquer `font-variant-numeric: tabular-nums` sur colonnes monetaire
- [ ] Tester etat vide, etat rempli, et format des montants

## Dev Notes

- Eviter toute logique monetaire custom cote UI: affichage seulement, source de verite cote API.
- Standardiser le rendu monetaire via `MoneyDisplay`.

### Project Structure Notes

- `src/app/(app)/prestations/page.tsx`
- `src/components/shared/resource-empty-state.tsx`
- `src/components/shared/money-display.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 3.2, UX-DR9/11/19)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (`MoneyDisplay`, EUR, tabular nums)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour la liste prestations.

### File List

- `_bmad-output/implementation-artifacts/3-2-frontend-service-list-empty-state.md`
