# Story 7.1: Upload or replace invoice logo

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want to upload or replace a bounded logo image for my account,
So that my brand can appear on future PDF invoices.

## Acceptance Criteria

1. **Given** an image within **documented** format (e.g. PNG, JPEG, WebP) and **max dimensions / file size** (team defaults e.g. ≤ 2 MB, ≤ 2048 px), **when** I upload, **then** the file is stored **scoped to my user only** and I see a **preview** in settings (FR41, FR4, NFR-S3).
2. **Given** an invalid type or oversized file, **when** I upload, **then** the API returns validation error and the UI shows a **short French message** (FR29, UX-DR16).
3. **Given** I replace the logo, **when** upload succeeds, **then** the previous binary is deleted or orphaned files are garbage-collected per team policy—**document** storage backend (local disk vs object storage).
4. **Given** unauthenticated access, **when** I POST upload, **then** 401.

## Tasks / Subtasks

- [ ] Schema: add `logo_storage_key` / `logo_url` / `logo_updated_at` on `freelancer_profiles` or dedicated `user_assets` table—**document ERD update** in `docs/database-schema.md`.
- [ ] Nest: `POST /api/v1/profile/logo` or `PATCH` multipart; virus scan optional out of scope—**document** if skipped.
- [ ] Validate magic bytes, not only extension; strip EXIF if required by policy (optional).
- [ ] Next: settings page section “Logo facture”; preview; replace flow (UX-DR17 secondary actions).
- [ ] Tests: multipart upload happy path; rejection too large; tenant cannot set another user’s logo.

## Dev Notes

- **Secrets:** object storage credentials in env only (NFR-S2).
- **Next story 7.2** reads this asset in PDF pipeline—expose internal file path or signed internal URL for Nest PDF module only (no public hotlink unless intended).

### Technical Requirements

- Do not serve user-controlled SVG without sanitization if SVG allowed—**prefer raster only** for v1 logo support.

### Architecture Compliance

- [Source: `architecture.md` — `freelancer_profiles`, tenant isolation]

### File Structure (guidance)

- `backend/src/modules/users/` or `freelancer-profile/` — logo handlers
- `frontend/src/components/features/profile/LogoUpload.tsx`

### Testing Requirements

- E2e: upload → GET profile returns logo metadata.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 7.1
- `_bmad-output/planning-artifacts/prd.md` — FR41
- `docs/v2.md` — PDF branding

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
