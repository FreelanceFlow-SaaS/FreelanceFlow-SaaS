# Story 4.4: Frontend - Invoice list, empty state, and navigation

Status: review

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

- [x] Implementer page liste factures
- [x] Gerer etat vide via `ResourceEmptyState`
- [x] Ajouter tri basique date/statut
- [x] Ajouter deep links vers detail facture
- [x] Tester navigation, tri et etat vide

## Dev Notes

- Garder le tri minimal V1, sans surcomplexifier (pas de filtre avance obligatoire).
- Assurer coherence des badges statut avec texte explicite (pas couleur seule).

### Project Structure Notes

- `src/app/(app)/factures/page.tsx`
- `src/modules/invoices/components/invoices-list.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.4, FR21, UX-DR9/16/21)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (listes, badges, empty state)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 4)

### Debug Log References

- N/A

### Completion Notes List

- `GET /api/v1/invoices`, tri client (date d'emission / statut), liens vers `/factures/[id]`, badge statut avec libelle FR, colonne TTC avec `MoneyDisplay`.
- Commit: `feat(#9): Add invoice list, empty state, sort, and navigation`.

### File List

- `frontend/src/lib/api/invoices-api.ts`
- `frontend/src/modules/invoices/utils/invoice-i18n.ts`
- `frontend/src/modules/invoices/components/invoices-list.tsx`
- `frontend/src/modules/invoices/components/invoices-list.test.tsx`
- `frontend/src/app/(app)/factures/page.tsx`
- `_bmad-output/implementation-artifacts/4-4-frontend-invoice-list-empty-state-navigation.md`

## Change Log

- 2026-04-11: Implementation story 4.4 (branche `feature/epic-4-invoices`).
