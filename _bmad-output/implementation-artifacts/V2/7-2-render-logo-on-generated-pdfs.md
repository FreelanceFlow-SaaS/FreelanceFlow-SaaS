# Story 7.2: Render logo on generated PDFs

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want my current logo to appear on generated invoice PDFs,
So that documents look professional without hiding mandatory invoice fields.

## Acceptance Criteria

1. **Given** a stored logo for my account, **when** I generate PDF for an invoice I own, **then** the logo renders in a **header** region sized per template (e.g. max height 48–64 pt) (FR42, FR26).
2. **Given** mandatory blocks (seller, client, lines, totals, identifiers), **when** PDF is rendered, **then** logo **does not overlap** legal/minimum content—if space is tight, logo scales down or moves per template rules (FR42).
3. **Given** no logo uploaded, **when** I generate PDF, **then** layout matches current V1 behaviour (no broken image icon).
4. **Given** PDF parity tests from Epic 5, **when** CI runs, **then** tests still pass; add or update golden PDF/hash for fixture **with** logo (FR27 alignment for amounts unchanged).

## Tasks / Subtasks

- [ ] PDF module: load logo buffer from storage key resolved via `userId` on invoice load path.
- [ ] HTML template or PDFKit image draw—**same engine** as existing PDF story.
- [ ] Cache interaction: do **not** cache PDF bytes in Redis (architecture); optional cache bust via `logo_updated_at` in template hash if metadata caching exists later.
- [ ] Tests: visual regression or byte length sanity; unit for “logo absent” branch.

## Dev Notes

- **Depends on:** Story 7.1 storage path; Epic 5 PDF pipeline.
- **Performance:** Image decode is local; acceptable for NFR-P3 with existing loading UI.

### Technical Requirements

- Resolve file async before Puppeteer/PDF render; fail gracefully with French error if file missing on disk (edge case after manual delete).

### Architecture Compliance

- [Source: `architecture.md` — PDF from persisted invoice + userId; no PDF in Redis]

### File Structure (guidance)

- `backend/src/modules/pdf/*` — inject logo into template
- Shared asset loader utility

### Testing Requirements

- Fixture user with small PNG; assert PDF contains expected marker or image XObject if inspectable.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 7.2
- `_bmad-output/planning-artifacts/prd.md` — FR42, FR26, FR27

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
