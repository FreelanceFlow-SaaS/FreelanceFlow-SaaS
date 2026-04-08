# Story 3.3: Frontend - Create and edit service

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux creer et modifier une prestation,  
Afin de garder mes titres et tarifs a jour.

## Acceptance Criteria

1. **Given** des formulaires prestation en francais  
   **When** l'utilisateur soumet des donnees valides  
   **Then** les feedbacks suivent les regles UX de validation/action/loading.
2. **Given** un titre ou un taux invalide  
   **When** la soumission echoue  
   **Then** les erreurs champs sont affichees sans effacer les autres champs.

## Tasks / Subtasks

- [ ] Implementer pages create/edit prestation
- [ ] Construire formulaire prestation reutilisable (title, hourlyRate)
- [ ] Valider et mapper erreurs backend FR (`details`)
- [ ] Conserver les valeurs non invalides apres echec
- [ ] Ajouter feedback succes (toast/inline) et mise a jour liste
- [ ] Ecrire tests succes et echec validation

## Dev Notes

- Utiliser un type decimal-safe en transport/affichage (string decimal ou convention backend), jamais de float JS pour persistance.
- Unifier les patterns de formulaire avec Story 2.3 pour limiter regressions.

### Project Structure Notes

- `src/app/(app)/prestations/new/page.tsx`
- `src/app/(app)/prestations/[id]/edit/page.tsx`
- `src/modules/services/components/service-form.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 3.3, FR29, UX-DR12/14/15)
- `_bmad-output/planning-artifacts/prd.md` (FR11, FR13)
- `_bmad-output/planning-artifacts/architecture.md` (regles numeriques et JSON conventions)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour create/edit prestation.

### File List

- `_bmad-output/implementation-artifacts/3-3-frontend-create-edit-service.md`
