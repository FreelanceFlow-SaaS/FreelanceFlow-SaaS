# Story 2.4: Frontend - Delete client with confirmation

Status: review

## Story

En tant qu'utilisateur authentifie,  
Je veux supprimer un client avec confirmation,  
Afin d'eviter les suppressions accidentelles.

## Acceptance Criteria

1. **Given** une ligne ou un detail client  
   **When** l'utilisateur clique supprimer  
   **Then** une `Dialog` Radix de confirmation FR avec style destructif apparait.
2. **Given** une erreur API  
   **When** la suppression echoue  
   **Then** le feedback est affiche en francais.
3. **Given** une suppression reussie  
   **When** l'action se termine  
   **Then** la liste est rafraichie.

## Tasks / Subtasks

- [x] Ajouter action supprimer sur liste/detail client
- [x] Implementer dialog de confirmation destructif (Radix Dialog)
- [x] Brancher mutation delete + loading state
- [x] Afficher feedback succes/erreur FR et rafraichir la liste
- [x] Couvrir test d'integration du flux complet

## Dev Notes

- Toujours exiger une confirmation explicite pour action destructive.
- Reutiliser le composant dialog destructif sur services/factures pour coherence UX.

### Project Structure Notes

- `src/modules/clients/components/delete-client-dialog.tsx`
- `src/app/(app)/clients/page.tsx`
- `src/components/ui/dialog.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 2.4, FR10, UX-DR14)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (dialogs destructifs, feedback erreurs)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 2)

### Debug Log References

- N/A

### Completion Notes List

- `@radix-ui/react-dialog`, primitifs encapsules dans `components/ui/dialog.tsx`, `DELETE /clients/:id` (204), message d'erreur inline dans le dialog, rafraichissement liste + `?deleted=1` pour bandeau.
- Commit: `feat(#5): Add delete client confirmation dialog`.

### File List

- `frontend/package.json`
- `frontend/package-lock.json`
- `frontend/src/components/ui/dialog.tsx`
- `frontend/src/modules/clients/components/delete-client-dialog.tsx`
- `frontend/src/modules/clients/components/delete-client-dialog.test.tsx`
- `frontend/src/modules/clients/components/clients-list.tsx`
- `frontend/src/lib/api/clients-api.ts` (deleteClient)
- `_bmad-output/implementation-artifacts/2-4-frontend-delete-client-confirmation.md`

## Change Log

- 2026-04-11: Implementation story 2.4.
