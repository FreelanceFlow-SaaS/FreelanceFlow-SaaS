# Story 4.6: Frontend - Invoice line table, service picker, and totals panel

Status: review

## Story

En tant qu'utilisateur authentifie,  
Je veux editer les lignes et voir HT/TVA/TTC depuis le serveur,  
Afin de faire confiance aux montants avant PDF.

## Acceptance Criteria

1. **Given** editeur de lignes et panneau synthese  
   **When** ajout/suppression/reordonnancement et choix prestation  
   **Then** `MoneyDisplay`, tabular nums, EUR ; mode lecture seule hors brouillon ; aide snapshot ; totaux alignes API.

## Tasks / Subtasks

- [x] Editeur lignes brouillon (ajout, retirer, monter/descendre)
- [x] Select prestation -> prefill titre + tarif (snapshot explique en copy)
- [x] `PATCH /invoices/:id/lines` avec validation locale
- [x] Panneau synthese avec totaux serveur
- [x] Table lecture seule hors brouillon

## Dev Notes

- Alignement strict sur le remplacement atomique des lignes cote API.

### Project Structure Notes

- `src/modules/invoices/components/invoice-lines-editor.tsx`
- `src/modules/invoices/components/invoice-totals-panel.tsx`
- `src/modules/invoices/components/invoice-detail-view.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.6, FR17/19/23, UX-DR5/6/7/11/22)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 4)

### Completion Notes List

- `InvoiceLinesEditor` : fetch prestations, lignes mappees depuis la facture, `Enregistrer les lignes`.
- Commit: `feat(#11): Add invoice line editor, service picker, and line save`.

### File List

- `frontend/src/modules/invoices/components/invoice-lines-editor.tsx`
- `frontend/src/modules/invoices/components/invoice-detail-view.tsx`
- `frontend/src/lib/api/invoices-api.ts`
- `frontend/src/lib/api/services-api.ts`
- `_bmad-output/implementation-artifacts/4-6-frontend-invoice-lines-service-picker-totals.md`

## Change Log

- 2026-04-11: Implementation story 4.6.
