# Story 4.6: Frontend - Invoice line table, service picker, and totals panel

Status: ready-for-dev

## Story

En tant qu'utilisateur authentifie,  
Je veux editer les lignes et voir HT/TVA/TTC renvoyes par le serveur,  
Afin d'avoir confiance dans les montants avant PDF.

## Acceptance Criteria

1. **Given** `InvoiceLineTable` / editeur et `InvoiceTotalsPanel` ("Synthese")  
   **When** l'utilisateur ajoute/supprime/reordonne des lignes, choisit une prestation et saisit des quantites  
   **Then** les montants sont affiches via `MoneyDisplay` en EUR avec chiffres tabulaires.
2. **Given** un statut non editable  
   **When** l'utilisateur ouvre la facture  
   **Then** le mode read-only s'applique avec texte d'aide explicatif.
3. **Given** la logique snapshot des lignes  
   **When** une ligne est creee depuis un service  
   **Then** une aide inline courte explique la semantique.
4. **Given** save/refetch  
   **When** l'operation se termine  
   **Then** les totaux affiches correspondent aux totaux API persistes.

## Tasks / Subtasks

- [ ] Implementer `InvoiceLineTable` (CRUD lignes + reorder si scope)
- [ ] Integrer selection de service avec prefill description + prix unitaire HT
- [ ] Implementer `InvoiceTotalsPanel` sticky avec `MoneyDisplay`
- [ ] Gerer mode read-only selon statut facture
- [ ] Ajouter aide inline "snapshot service -> ligne"
- [ ] Synchroniser les donnees apres save/refetch pour parite stricte
- [ ] Tester coherence des totaux et read-only

## Dev Notes

- Les calculs de reference restent server-side; le frontend affiche la verite API.
- Utiliser des identifiants stables pour lignes afin d'eviter erreurs de re-render/reorder.
- Priorite a la parite UI/API (FR19, FR23) pour eviter divergences avant PDF.

### Project Structure Notes

- `src/modules/invoices/components/invoice-line-table.tsx`
- `src/modules/invoices/components/invoice-totals-panel.tsx`
- `src/components/shared/money-display.tsx`

### References

- `_bmad-output/planning-artifacts/epics.md` (Story 4.6, FR17/19/23, UX-DR5/6/7/11/22)
- `_bmad-output/planning-artifacts/ux-design-specification.md` (`InvoiceLineTable`, `InvoiceTotalsPanel`, `MoneyDisplay`)
- `_bmad-output/planning-artifacts/prd.md` (totaux persistes et parite PDF)

## Dev Agent Record

### Agent Model Used

gpt-5.3-codex-low

### Debug Log References

- N/A

### Completion Notes List

- Story context cree pour edition lignes et synthese facture.

### File List

- `_bmad-output/implementation-artifacts/4-6-frontend-invoice-lines-service-picker-totals.md`
