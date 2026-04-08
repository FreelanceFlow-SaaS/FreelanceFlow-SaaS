# Story 5.2: Frontend - PDF trigger, loading state, and download

Status: ready-for-dev

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

- [ ] Ajouter action "Generer PDF" sur detail facture
- [ ] Gerer etat loading/disabled + libelle FR
- [ ] Implementer telechargement fiable (blob/url) apres succes
- [ ] Gerer erreurs API avec feedback FR et bouton retry
- [ ] Ajouter region `aria-live` pour annonce de resultat
- [ ] Tester flux success/failure/retentative

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

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour trigger/generation/telechargement PDF.

### File List

- `_bmad-output/implementation-artifacts/5-2-frontend-pdf-trigger-loading-download.md`
