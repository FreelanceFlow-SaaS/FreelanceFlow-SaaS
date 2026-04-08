# Story 2.4: Frontend - Delete client with confirmation

Status: ready-for-dev

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

- [ ] Ajouter action supprimer sur liste/detail client
- [ ] Implementer dialog de confirmation destructif (Radix Dialog)
- [ ] Brancher mutation delete + loading state
- [ ] Afficher feedback succes/erreur FR et rafraichir la liste
- [ ] Couvrir test d'integration du flux complet

## Dev Notes

- Toujours exiger une confirmation explicite pour action destructive.
- Reutiliser le composant dialog destructif sur services/factures pour coherence UX.

### Project Structure Notes

- `src/modules/clients/components/delete-client-dialog.tsx`
- `src/app/(app)/clients/page.tsx`
- `src/app/(app)/clients/[id]/*`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 2.4, FR10, UX-DR14)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (dialogs destructifs, feedback erreurs)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour suppression client securisee.

### File List

- `_bmad-output/implementation-artifacts/2-4-frontend-delete-client-confirmation.md`
