# Story 2.3: Frontend - Create and edit client

Status: review

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

- [x] Implementer les routes create/edit clients
  - [x] `src/app/(app)/clients/new/page.tsx`
  - [x] `src/app/(app)/clients/[id]/edit/page.tsx`
- [x] Construire un formulaire client reutilisable
  - [x] Champs: name, email, company, address
  - [x] Validation locale + mapping erreur backend FR (`details`)
  - [x] `aria-invalid`, `aria-describedby`
- [x] Gerer mutation et retour UX
  - [x] Success toast/inline + refresh liste
  - [x] Bouton principal unique selon contexte
- [x] Tests
  - [x] Cas succes create/edit
  - [x] Cas echec validation sans perte des champs valides

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

Cursor agent (Epic 2)

### Debug Log References

- N/A

### Completion Notes List

- `ClientForm` mode `create` | `edit`, `POST/PATCH /clients`, redirection vers `/clients?created=1` ou `?updated=1`, bandeau de succes sur la liste (`useSearchParams` + `Suspense`).
- Commit: `feat(#4): Add client create and edit flows`.

### File List

- `frontend/src/modules/clients/components/client-form.tsx`
- `frontend/src/modules/clients/components/client-form.test.tsx`
- `frontend/src/app/(app)/clients/new/page.tsx`
- `frontend/src/app/(app)/clients/[id]/edit/page.tsx`
- `frontend/src/app/(app)/clients/page.tsx` (Suspense pour `useSearchParams`)
- `frontend/src/modules/clients/components/clients-list.tsx` (bandeaux succes)
- `frontend/src/lib/api/clients-api.ts` (create/update)
- `_bmad-output/implementation-artifacts/2-3-frontend-create-edit-client.md`

## Change Log

- 2026-04-11: Implementation story 2.3.
