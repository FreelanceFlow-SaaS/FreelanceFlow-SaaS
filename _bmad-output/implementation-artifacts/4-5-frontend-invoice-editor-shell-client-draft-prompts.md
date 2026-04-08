# Story 4.5: Frontend - Invoice editor shell, client selection, draft save, and prerequisite prompts

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux initier une facture et sauvegarder un brouillon,  
Afin de finaliser les lignes ensuite.

## Acceptance Criteria

1. **Given** la page create/edit facture  
   **When** profil vendeur ou client manquant  
   **Then** des prompts inline guident vers Profil vendeur ou Clients avant blocage.
2. **Given** un client selectionne  
   **When** l'utilisateur sauvegarde  
   **Then** le layout suit UX-DR5: deux colonnes a partir de `lg` (lignes + synthese sticky), empile en dessous.
3. **Given** interactions et erreurs  
   **When** l'utilisateur soumet  
   **Then** la copie FR, la hierarchie primaire et le mapping erreurs sont respectes.

## Tasks / Subtasks

- [ ] Construire la page editeur facture (create/edit)
- [ ] Integrer select client + prechecks prerequisites (profil/client)
- [ ] Implementer sauvegarde brouillon (mutation + loading + feedback)
- [ ] Mettre en place layout responsive (2 colonnes `lg+`, stack `<lg`)
- [ ] Mapper erreurs API en FR
- [ ] Tester parcours prerequisites et save draft

## Dev Notes

- Ne pas masquer les prerequis: afficher des CTA clairs vers pages manquantes.
- Garder le socle de page compatible avec les stories 4.6 et 4.7.

### Project Structure Notes

- `src/app/(app)/factures/new/page.tsx`
- `src/app/(app)/factures/[id]/edit/page.tsx`
- `src/modules/invoices/components/invoice-editor-shell.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.5, FR15, FR29, UX-DR5/12/14/21)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (layout editeur, copy FR, hierarchy actions)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour shell editeur facture et sauvegarde brouillon.

### File List

- `_bmad-output/implementation-artifacts/4-5-frontend-invoice-editor-shell-client-draft-prompts.md`
