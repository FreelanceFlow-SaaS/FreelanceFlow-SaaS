# Story 4.7: Frontend - Invoice status actions

Status: review

## Story

En tant qu'utilisateur authentifie,  
Je veux des actions conformes au statut de ma facture,  
Afin de n'effectuer que les transitions autorisees.

## Acceptance Criteria

1. **Given** chargement facture  
   **Then** transitions autorisees actives ; dialog FR pour confirmations ; badge statut avec texte ; loading sur mutation.

## Tasks / Subtasks

- [x] Composant actions statut (draft/sent -> paid/cancelled selon matrice serveur)
- [x] Dialog Radix pour chaque transition
- [x] Suppression facture (brouillon ou annulee) avec dialog
- [x] Bandeau liste `?deleted=1`
- [x] Tests dialog suppression et transition envoyee

## Dev Notes

- Matrice : draft -> sent | cancelled ; sent -> paid | cancelled ; paid/cancelled : aucune transition.

### Project Structure Notes

- `src/modules/invoices/components/invoice-status-actions.tsx`
- `src/modules/invoices/components/delete-invoice-dialog.tsx`
- `src/modules/invoices/components/invoice-detail-view.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.7, FR20, UX-DR8/14/15/18)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 4)

### Completion Notes List

- `InvoiceStatusActions` + `DeleteInvoiceDialog`, `PATCH /status`, `DELETE` si autorise.
- Commit: `feat(#12): Add invoice status transitions, confirmations, and delete`.

### File List

- `frontend/src/modules/invoices/components/invoice-status-actions.tsx`
- `frontend/src/modules/invoices/components/invoice-status-actions.test.tsx`
- `frontend/src/modules/invoices/components/delete-invoice-dialog.tsx`
- `frontend/src/modules/invoices/components/delete-invoice-dialog.test.tsx`
- `frontend/src/modules/invoices/components/invoice-detail-view.tsx`
- `frontend/src/modules/invoices/components/invoices-list.tsx`
- `_bmad-output/implementation-artifacts/4-7-frontend-invoice-status-actions.md`

## Change Log

- 2026-04-11: Implementation story 4.7.
