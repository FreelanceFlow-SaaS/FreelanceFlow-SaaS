# Story 4.5: Frontend - Invoice editor shell, client selection, draft save, and prerequisite prompts

Status: review

## Story

En tant qu'utilisateur authentifie,  
Je veux demarrer une facture et enregistrer un brouillon,  
Afin de completer les lignes ensuite.

## Acceptance Criteria

1. **Given** page creation/edition facture  
   **When** profil vendeur ou client manque  
   **Then** des invites pointent vers Profil vendeur ou Clients avant blocage.
2. **Given** choix client et sauvegarde  
   **Then** layout `lg+` deux colonnes (contenu + rail synthese) et pilee en dessous de `lg`, copy FR et erreurs mappees.

## Tasks / Subtasks

- [x] Page nouvelle facture avec selection client et dates
- [x] Prompts prerequis (profil minimal + clients)
- [x] Creation brouillon via API (au moins une ligne requise par le backend)
- [x] Page detail avec grille `lg` synthese + metadonnees brouillon
- [x] Tests creation minimale

## Dev Notes

- Le backend exige au moins une ligne a la creation : premiere ligne saisie sur le formulaire de creation, puis edition complete sur la fiche.

### Project Structure Notes

- `src/app/(app)/factures/new/page.tsx`
- `src/app/(app)/factures/[id]/page.tsx`
- `src/modules/invoices/components/create-invoice-form.tsx`
- `src/modules/invoices/components/invoice-detail-view.tsx`
- `src/modules/invoices/components/invoice-totals-panel.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.5, FR15, FR29, UX-DR5/12/14/21)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 4)

### Completion Notes List

- `CreateInvoiceForm` : profil (identite + adresse), liste clients, premiere ligne (description, qté, PU HT, TVA), `POST /invoices`.
- Fiche : `Synthèse` sticky, dates editables en brouillon (`PATCH` metadata).
- Commit: `feat(#10): Add invoice draft creation, detail shell, and totals panel`.

### File List

- `frontend/src/modules/invoices/components/create-invoice-form.tsx`
- `frontend/src/modules/invoices/components/create-invoice-form.test.tsx`
- `frontend/src/modules/invoices/components/invoice-detail-view.tsx`
- `frontend/src/modules/invoices/components/invoice-totals-panel.tsx`
- `frontend/src/app/(app)/factures/new/page.tsx`
- `frontend/src/app/(app)/factures/[id]/page.tsx`
- `frontend/src/lib/api/invoices-api.ts`
- `_bmad-output/implementation-artifacts/4-5-frontend-invoice-editor-shell-client-draft-prompts.md`

## Change Log

- 2026-04-11: Implementation story 4.5.
