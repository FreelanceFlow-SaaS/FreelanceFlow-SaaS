# Story 6.2: Export invoices to UTF-8 CSV

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want to download my invoices as UTF-8 CSV,
So that amounts and identifiers match my books and my accountant’s templates.

## Acceptance Criteria

1. **Given** invoices I own, **when** I export, **then** each row includes identifiers and monetary fields **exactly as persisted** on `invoices` (and denormalized client label if included)—no client-side recomputation of HT/TVA/TTC for the export row (FR35, FR23, FR4).
2. **Given** README or API doc, **when** I look up `export-schema=v1`, **then** invoice export columns are versioned alongside client export (can be `export-schema=v1` with separate subsection or `invoice-export-schema=v1`) (FR35, PRD appendix).
3. **Given** mixed statuses, **when** I export, **then** all my invoices appear unless product intentionally filters (default: **all statuses**); revenue interpretation for dashboard is **not** required in this story—only faithful column dump (FR35).
4. **Given** another user’s invoice id, **when** I request export or single-invoice leakage via crafted query, **then** no cross-tenant data (NFR-S3).

## Tasks / Subtasks

- [ ] Document column set: at minimum `id`, `invoiceNumber`, `clientId`, `status`, `issueDate`, `dueDate`, `currency`, `totalHt`, `totalVat`, `totalTtc`, `createdAt`, `updatedAt`; add `clientName` / `company` if useful—**freeze in README**.
- [ ] Nest: `/api/v1/invoices/export` (or equivalent); join client for display fields; DECIMAL serialized as fixed decimal strings (no JS float).
- [ ] Next: Export action on invoice list; loading and error states (UX-DR16).
- [ ] Tests: decimal formatting; tenant isolation; row count matches DB for fixture user.

## Dev Notes

- Align numeric string format with French locale expectations where relevant (comma vs dot)—**CSV convention**: team may use **comma as decimal separator** for FR accountants OR **dot** for RFC-style; **document one choice** in schema appendix.
- If `paidAt` is added in a migration for Story 6.3, optionally include it in invoice CSV in same release or follow-up—document.

### Technical Requirements

- Use same auth as other invoice reads; reuse invoice repository methods with `userId` filter.

### Architecture Compliance

- [Source: `architecture.md` — money as non-float in persistence; camelCase JSON (CSV header may mirror)]

### File Structure (guidance)

- `backend/src/modules/invoices/*` — export method
- `frontend/src/components/features/invoices/*` — CTA wiring

### Testing Requirements

- Golden file or snapshot for two invoices with VAT decimals.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 6.2
- `_bmad-output/planning-artifacts/prd.md` — FR35
- `docs/database-schema.md` — `invoices`

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
