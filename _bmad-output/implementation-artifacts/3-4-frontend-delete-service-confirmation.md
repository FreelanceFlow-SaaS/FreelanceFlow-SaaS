# Story 3.4: Frontend - Delete service with confirmation

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux supprimer une prestation en toute securite,  
Afin de retirer les offres obsoletes sans erreur.

## Acceptance Criteria

1. **Given** une action de suppression  
   **When** l'utilisateur confirme dans une dialog destructive  
   **Then** la prestation est supprimee et la liste est mise a jour.

## Tasks / Subtasks

- [ ] Ajouter action supprimer dans la liste/detail prestation
- [ ] Implementer dialog de confirmation FR (style destructif)
- [ ] Brancher mutation delete + loading + erreurs FR
- [ ] Rafraichir liste sur succes
- [ ] Ajouter test d'integration suppression/confirmee-annulee

## Dev Notes

- Factoriser le composant de suppression avec celui des clients si possible.
- Garder une terminologie FR consistante pour les actions destructives.

### Project Structure Notes

- `src/modules/services/components/delete-service-dialog.tsx`
- `src/app/(app)/prestations/page.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 3.4, FR14, UX-DR14)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (confirmation destructive)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour suppression prestation.

### File List

- `_bmad-output/implementation-artifacts/3-4-frontend-delete-service-confirmation.md`
