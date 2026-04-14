# Story 6.3: Dashboard totals — revenue (paid TTC rules) and invoice count

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want the dashboard to show total revenue and total invoice count from my stored data,
So that I can see business volume at a glance without placeholder numbers.

## Acceptance Criteria

1. **Given** invoices in mixed statuses, **when** I open the dashboard, **then** **revenue** sums **TTC in EUR** only for invoices with **`status = paid`**, using attribution date **`paidAt` if present, else `updatedAt`**—document which field ships in README once chosen (FR36, FR40, PRD Measurement defaults).
2. **Given** the same rules, **when** I compare dashboard revenue to a manual sum of paid invoices from DB, **then** they match (FR40).
3. **Given** my account, **when** the dashboard loads, **then** **invoice count** is the **total number of invoices** for my `userId` (all statuses) unless the team documents a different rule—**default: all** (FR37, epics Story 6.3).
4. **Given** no paid invoices, **when** I view the dashboard, **then** revenue shows **0 EUR** clearly (MoneyDisplay / tabular nums UX-DR10).
5. **Given** unauthenticated request, **when** I hit dashboard API, **then** 401 (FR4).

## Tasks / Subtasks

- [ ] If `paidAt` missing in schema: add nullable `paid_at` timestamptz on `invoices`, set on transition to `paid` in invoice status service (coordinate with Epic 4 status story if not done).
- [ ] Nest: aggregation query or materialized path—`GET /api/v1/dashboard/summary` (name as team prefers) returning `{ totalRevenueTtc, invoiceCount, ... }` with DECIMAL as string or structured minor units—**consistent with existing API DTOs**.
- [ ] Next: dashboard route (new or extend home); use `Intl` for EUR display; French copy (UX-DR18).
- [ ] Tests: fixtures for draft/sent/paid/cancelled; assert revenue includes only paid TTC; count equals row count.

## Dev Notes

- **Dependency:** Invoice statuses and persisted totals from Epic 4 must be trustworthy.
- **Cache:** Story 8.3 may wrap this endpoint—implement **correctness first**, then integrate invalidation; or stub without cache in 6.3 and add cache in 8.3.

### Technical Requirements

- PostgreSQL `SUM` on numeric type; filter `user_id = :authUserId`.
- Document revenue rule in `_bmad-output/planning-artifacts/prd.md` appendix alignment—no drift from CSV if later “revenue export” is added.

### Architecture Compliance

- Dashboard is not a special case for tenant isolation (NFR-S3).

### File Structure (guidance)

- `backend/src/modules/dashboard/` or `invoices/dashboard.service.ts`
- `frontend/src/app/(app)/dashboard/page.tsx` (if new nav item—coordinate UX: sidebar may add “Tableau de bord” per product)

### Testing Requirements

- Integration test with known sums; edge case: paid with TTC 0 if allowed.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 6.3
- `_bmad-output/planning-artifacts/prd.md` — FR36, FR37, FR40; Measurement table (revenue, invoice count)
- `docs/v2.md` — dashboard KPIs

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
