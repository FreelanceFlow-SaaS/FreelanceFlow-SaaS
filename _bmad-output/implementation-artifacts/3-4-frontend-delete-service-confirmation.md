# Story 3.4: Frontend - Delete service with confirmation

Status: review

## Story

En tant qu'utilisateur authentifie,  
Je veux supprimer une prestation en toute securite,  
Afin de retirer les offres obsoletes sans erreur.

## Acceptance Criteria

1. **Given** une action de suppression  
   **When** l'utilisateur confirme dans une dialog destructive  
   **Then** la prestation est supprimee et la liste est mise a jour.

## Tasks / Subtasks

- [x] Ajouter action supprimer dans la liste/detail prestation
- [x] Implementer dialog de confirmation FR (style destructif)
- [x] Brancher mutation delete + loading + erreurs FR
- [x] Rafraichir liste sur succes
- [x] Ajouter test d'integration suppression/confirmee-annulee

## Dev Notes

- Factoriser le composant de suppression avec celui des clients si possible.
- Garder une terminologie FR consistante pour les actions destructives.

### Project Structure Notes

- `src/modules/services/components/delete-service-dialog.tsx`
- `src/app/(app)/prestations/page.tsx`
- `src/components/ui/dialog.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 3.4, FR14, UX-DR14)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (confirmation destructive)

## Dev Agent Record

### Agent Model Used

Cursor agent (Epic 3)

### Debug Log References

- N/A

### Completion Notes List

- `DELETE /services/:id` (204), dialog Radix alignee clients, rafraichissement + `?deleted=1`.
- Commit: `feat(#8): Add delete service confirmation dialog`.

### File List

- `frontend/src/modules/services/components/delete-service-dialog.tsx`
- `frontend/src/modules/services/components/delete-service-dialog.test.tsx`
- `frontend/src/modules/services/components/services-list.tsx`
- `frontend/src/lib/api/services-api.ts`
- `_bmad-output/implementation-artifacts/3-4-frontend-delete-service-confirmation.md`

## Change Log

- 2026-04-11: Implementation story 3.4.
