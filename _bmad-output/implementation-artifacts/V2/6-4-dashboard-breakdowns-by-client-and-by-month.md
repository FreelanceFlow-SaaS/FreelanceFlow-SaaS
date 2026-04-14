# Story 6.4: Dashboard breakdowns by client and by month

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want to see revenue broken down by client and by calendar month,
So that I can spot concentration risk and seasonality.

## Acceptance Criteria

1. **Given** paid invoices across multiple clients and months, **when** I load the dashboard, **then** **by-client** revenue uses the **same paid / TTC / attribution date rules as Story 6.3** (FR38, FR40, PRD appendix).
2. **Given** paid invoices, **when** I view **by-month** revenue, **then** months are **calendar months** in **`Europe/Paris`** unless README documents **UTC** instead—same choice for UI and future CSV extensions (FR39, PRD appendix).
3. **Given** a client with zero paid invoices, **when** breakdowns render, **then** they show **0** or are omitted—**document UX choice** (empty slice vs explicit 0).
4. **Given** large history, **when** dashboard loads, **then** response remains within team latency budget (NFR-P1); optional limit “last 24 months” documented if needed.

## Tasks / Subtasks

- [ ] Extend `GET /api/v1/dashboard/summary` (or companion endpoint) with `revenueByClient: { clientId, label, totalTtc }[]` and `revenueByMonth: { yearMonth, totalTtc }[]`.
- [ ] SQL: `GROUP BY` client_id; month bucket using `AT TIME ZONE 'Europe/Paris'` on attribution date—verify index strategy (`status`, `user_id`, date).
- [ ] Next: charts or tables using semantic tokens; **tabular-nums**; French month labels via `Intl` (UX-DR10, DR18).
- [ ] Tests: known fixtures spanning year boundary (Dec/Jan) in Paris TZ.

## Dev Notes

- **Previous story:** Reuse 6.3 revenue definition exactly—extract shared pure function or SQL view to avoid drift.
- **Visualization:** Bar chart optional; table is sufficient for acceptance if numbers are correct.

### Technical Requirements

- Do not leak other users’ client names in aggregations (join always filtered by invoice.user_id).

### Architecture Compliance

- [Source: `architecture.md` — EUR, tenant isolation]

### File Structure (guidance)

- Same module as 6.3; shared `DashboardMetricsService`.

### Testing Requirements

- Unit tests for month bucketing edge cases (DST not usually affecting month boundaries).

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 6.4
- `_bmad-output/planning-artifacts/prd.md` — FR38, FR39, FR40

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
