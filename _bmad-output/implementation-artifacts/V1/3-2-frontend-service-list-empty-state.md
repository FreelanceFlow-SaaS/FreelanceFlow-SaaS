# Story 3.2: Frontend - Service list and empty state

Status: review

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

- [x] Implementer la page liste prestations
- [x] Reutiliser `ResourceEmptyState` pour etat vide
- [x] Afficher les montants via `MoneyDisplay` en locale FR/EUR
- [x] Appliquer `font-variant-numeric: tabular-nums` sur colonnes monetaire
- [x] Tester etat vide, etat rempli, et format des montants

## Dev Notes

- Eviter toute logique monetaire custom cote UI: affichage seulement, source de verite cote API.
- Standardiser le rendu monetaire via `MoneyDisplay`.

### Project Structure Notes

- `src/app/(app)/prestations/page.tsx`
- `src/components/shared/resource-empty-state.tsx`
- `src/components/shared/money-display.tsx`
- `src/modules/services/components/services-list.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 3.2, UX-DR9/11/19)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (`MoneyDisplay`, EUR, tabular nums)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 3)

### Debug Log References

- N/A

### Completion Notes List

- `GET /api/v1/services`, skeleton, tableau titre + taux HT avec `MoneyDisplay` et `tabular-nums`, liens vers `/prestations/[id]/edit`.
- Commit: `feat(#6): Add service list, empty state, and MoneyDisplay`.

### File List

- `frontend/src/lib/api/services-api.ts`
- `frontend/src/components/shared/money-display.tsx`
- `frontend/src/components/shared/money-display.test.tsx`
- `frontend/src/modules/services/components/services-list.tsx`
- `frontend/src/modules/services/components/services-list.test.tsx`
- `frontend/src/app/(app)/prestations/page.tsx`
- `_bmad-output/implementation-artifacts/3-2-frontend-service-list-empty-state.md`

## Change Log

- 2026-04-11: Implementation story 3.2 (branche `feature/epic-3-billable-services`).
