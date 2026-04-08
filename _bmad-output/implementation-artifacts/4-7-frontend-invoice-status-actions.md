# Story 4.7: Frontend - Invoice status actions

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux des actions coherentes avec le statut de facture,  
Afin d'appliquer uniquement les transitions autorisees.

## Acceptance Criteria

1. **Given** `InvoiceStatusActions`  
   **When** la facture charge  
   **Then** seules les transitions autorisees sont actives; les autres expliquent pourquoi via tooltip/helper.
2. **Given** une action destructive (ex: annuler)  
   **When** l'utilisateur la declenche  
   **Then** une dialog de confirmation FR est imposee.
3. **Given** l'affichage du statut  
   **When** le badge est rendu  
   **Then** il combine texte + couleur (jamais couleur seule).
4. **Given** une mutation de statut  
   **When** elle est en cours ou terminee  
   **Then** l'UI montre loading puis toast de succes.

## Tasks / Subtasks

- [ ] Implementer composant `InvoiceStatusActions`
- [ ] Mapper matrice de transitions autorisees (depuis backend/API contract)
- [ ] Ajouter etats disabled + explication utilisateur
- [ ] Integrer confirmations destructives
- [ ] Ajouter badges statut accessibles (texte + couleur)
- [ ] Gerer loading/toast sur mutations
- [ ] Tester transitions autorisees/interdites

## Dev Notes

- La regle de transition est source backend: ne pas inventer de transitions cote frontend.
- Afficher des raisons explicites en FR pour les actions bloquees pour eviter confusion.

### Project Structure Notes

- `src/modules/invoices/components/invoice-status-actions.tsx`
- `src/modules/invoices/components/invoice-status-badge.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.7, FR20, UX-DR8/14/15/18)
- `_bmad-output/planning-artifacts/prd.md` (statuts draft/sent/paid/cancelled)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (actions destructives, badges, a11y)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour actions de statut facture.

### File List

- `_bmad-output/implementation-artifacts/4-7-frontend-invoice-status-actions.md`
