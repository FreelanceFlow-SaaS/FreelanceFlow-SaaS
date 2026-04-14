# Story 5.2: Frontend - PDF trigger, loading state, and download

Status: review

## Story

En tant qu'utilisateur authentifie,  
Je veux une progression visible et un telechargement fiable du PDF,  
Afin de confirmer que la generation s'est bien terminee.

## Acceptance Criteria

1. **Given** la page detail facture  
   **When** l'utilisateur declenche la generation PDF  
   **Then** le bouton affiche un etat "Generation en cours..." et devient disabled/loading.
2. **Given** un resultat de generation  
   **When** succes  
   **Then** le telechargement est propose (ou blob ouvert).  
   **When** echec  
   **Then** un message FR + action de retry est affiche.
3. **Given** des technologies d'assistance  
   **When** la generation se termine  
   **Then** un `aria-live` (ou equivalent) annonce succes/echec.
4. **Given** la hierarchie des actions  
   **When** le bouton PDF est rendu  
   **Then** la priorisation visuelle respecte les regles UX des actions principales.

## Tasks / Subtasks

- [x] Ajouter action "Generer PDF" sur detail facture
- [x] Gerer etat loading/disabled + libelle FR
- [x] Implementer telechargement fiable (blob/url) apres succes
- [x] Gerer erreurs API avec feedback FR et bouton retry
- [x] Ajouter region `aria-live` pour annonce de resultat
- [x] Tester flux success/failure/retentative

## Dev Notes

- Le temps de generation PDF peut etre superieur au CRUD; l'UI doit rassurer sans freeze.
- Garder la logique de telechargement compatible navigateurs cibles de l'equipe.

### Project Structure Notes

- `src/app/(app)/factures/[id]/page.tsx`
- `src/modules/invoices/components/invoice-pdf-actions.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 5.2, FR25, NFR3, UX-DR12/13/14/18)
- `_bmad-output/planning-artifacts/prd.md` (FR24/25/27, exigences PDF)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (PDF feedback, `aria-live`, hierarchy actions)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 5)

### Debug Log References

- N/A

### Completion Notes List

- Client `GET /api/v1/pdf/invoices/:id` (Bearer), blob + lien telechargement `facture-{numero}.pdf`.
- Bouton `variant="outline"` pour rester secondaire par rapport aux actions de statut (UX-DR14).
- Zone `aria-live="polite"` + focus pour annonce succes/echec (UX-DR13).
- Commit: `feat(#13): Add invoice PDF download with loading, aria-live, and retry`.

### File List

- `frontend/src/lib/api/pdf-api.ts`
- `frontend/src/modules/invoices/components/invoice-pdf-actions.tsx`
- `frontend/src/modules/invoices/components/invoice-pdf-actions.test.tsx`
- `frontend/src/modules/invoices/components/invoice-detail-view.tsx`
- `_bmad-output/implementation-artifacts/5-2-frontend-pdf-trigger-loading-download.md`

## Change Log

- 2026-04-11: Implementation story 5.2 (branche `feature/epic-5-invoice-pdf`).
