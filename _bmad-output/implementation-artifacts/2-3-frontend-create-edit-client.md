# Story 2.3: Frontend - Create and edit client

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux creer et modifier un client,  
Afin de garder mon annuaire a jour.

## Acceptance Criteria

1. **Given** les formulaires client  
   **When** l'utilisateur soumet des donnees valides  
   **Then** un feedback de succes apparait (toast/inline) et la liste est mise a jour.
2. **Given** des erreurs de validation  
   **When** la soumission echoue  
   **Then** les erreurs champ par champ, `aria-invalid`, et la preservation des champs valides sont appliquees.
3. **Given** chaque vue  
   **When** l'utilisateur agit  
   **Then** une seule action primaire claire est proposee.

## Tasks / Subtasks

- [ ] Implementer les routes create/edit clients
  - [ ] `src/app/(app)/clients/new/page.tsx`
  - [ ] `src/app/(app)/clients/[id]/edit/page.tsx`
- [ ] Construire un formulaire client reutilisable
  - [ ] Champs: name, email, company, address
  - [ ] Validation locale + mapping erreur backend FR (`details`)
  - [ ] `aria-invalid`, `aria-describedby`
- [ ] Gerer mutation et retour UX
  - [ ] Success toast/inline + refresh liste
  - [ ] Bouton principal unique selon contexte
- [ ] Tests
  - [ ] Cas succes create/edit
  - [ ] Cas echec validation sans perte des champs valides

## Dev Notes

- Ne pas dupliquer create/edit: factoriser le formulaire et differencier via props/mode.
- Garder les messages d'erreur courts et actionnables en francais.
- Respecter le schema d'erreur API unifie pour rester coherent entre modules.

### Project Structure Notes

- `src/app/(app)/clients/new/page.tsx`
- `src/app/(app)/clients/[id]/edit/page.tsx`
- `src/modules/clients/components/client-form.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 2.3, UX-DR12/14/15, FR29)
- `_bmad-output/planning-artifacts/prd.md` (FR7, FR9)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (forms, erreurs API, a11y)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour create/edit client.

### File List

- `_bmad-output/implementation-artifacts/2-3-frontend-create-edit-client.md`
