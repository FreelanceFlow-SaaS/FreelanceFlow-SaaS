# Story 6.1: Export clients to UTF-8 CSV

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want to download my client directory as a UTF-8 CSV file,
So that I can reuse the data in spreadsheets or share it with my accountant.

## Acceptance Criteria

1. **Given** I am authenticated and own one or more clients, **when** I trigger export (UI control and/or `GET` endpoint returning `text/csv` or file download), **then** the response body is **UTF-8** with a **header row** and one row per client scoped to my `userId` only (FR34, FR4, NFR-S3).
2. **Given** the export contract, **when** a reviewer reads README or OpenAPI, **then** they find **`export-schema=v1`** (or current version) documenting **column names, order, and types** for the clients export (FR34, PRD Measurement appendix).
3. **Given** another user’s client ids, **when** I call the export API, **then** I never see their rows (tenant isolation).
4. **Given** special characters in names or addresses, **when** I open the file in Excel/LibreOffice with UTF-8, **then** characters render correctly (BOM optional; document if used).

## Tasks / Subtasks

- [ ] Define frozen column set for `export-schema=v1` (e.g. `id`, `name`, `email`, `company`, `address`, `createdAt`, `updatedAt` in camelCase or snake_case—**pick one**, document, stay consistent with JSON API casing decision).
- [ ] Nest: authenticated route under `/api/v1/clients/export` (or query flag on collection—document choice); stream or buffer CSV; set `Content-Disposition` attachment filename.
- [ ] Next: button on Clients list (and/or overflow menu) calling API with auth; handle errors with French toast per UX-DR16.
- [ ] Tests: unit for CSV row shaping; e2e or integration proving 403/404 for other tenant; snapshot of header row + one line.

## Dev Notes

- **Money:** N/A for clients export; keep addresses as plain text; no floats introduced.
- **Reuse:** `ClientsService` list query filtered by `userId`; do not duplicate tenant logic.
- **Performance:** OK to load all rows for MVP tenant size; if list is paginated elsewhere, export may use a dedicated query without pagination cap—document limit if any.

### Technical Requirements

- REST prefix `/api/v1`; unified error JSON (`statusCode`, `message`, optional `code`, `details`).
- CSV escaping for commas, quotes, newlines in `address` field.

### Architecture Compliance

- [Source: `_bmad-output/planning-artifacts/architecture.md` — API naming, Nest modules under `src/modules/clients/`]
- Tenant guard on every query (NFR-S3).

### File Structure (guidance)

- `backend/src/modules/clients/clients.controller.ts` (or dedicated export controller method)
- `backend/src/modules/clients/clients.service.ts` — `toCsvRow` helper or small `ClientCsvExporter` class
- `frontend/src/app/(app)/clients/page.tsx` or feature component — export CTA
- `frontend/src/lib/api/clients.ts` — typed export call

### Testing Requirements

- Backend: Jest test for CSV escaping and tenant filter.
- Optional: contract test that header matches README `export-schema=v1`.

### References

- `_bmad-output/planning-artifacts/epics.md` — Epic 6, Story 6.1
- `_bmad-output/planning-artifacts/prd.md` — FR34, Measurement & operational definitions (CSV)
- `docs/database-schema.md` — `clients` columns

## Dev Agent Record

### Agent Model Used

_(filled by dev agent)_

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
