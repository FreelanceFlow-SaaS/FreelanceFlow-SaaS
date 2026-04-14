# Story 8.3: Redis cache-aside for invoice lists and dashboard with invalidation

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want invoice lists and dashboard data to load quickly while staying correct after my writes,
So that caching satisfies FR43 and NFR-C1/C2 without stale drafts or cross-tenant leaks.

## Acceptance Criteria

1. **Given** invoice mutations that affect aggregates (lines, status, client linkage, deletes), **when** the write transaction **commits successfully**, **then** **affected cache keys** for that `userId` (invoice detail in stable band, list families, dashboard summary) are **invalidated or updated** **before** the HTTP response completes (FR43, NFR-C1, architecture **Invalidation** row).
2. **Given** a **`draft`** invoice, **when** reads occur, **then** the system **does not** rely on **long-lived** cache for financial snapshots / line payloads—**very short TTL or no cache** per architecture **Draft** row (FR43, architecture).
3. **Given** **`sent` / `paid` / `cancelled`** invoices, **when** serving **GET by id** or list/dashboard slices, **then** **cache-aside** via **shared Redis** is allowed after Postgres commit; keys include **tenant id** e.g. `user:{userId}:invoice:{invoiceId}` (NFR-C2, architecture key naming).
4. **Given** PRD default staleness, **when** another reader hits cache after my write, **then** **read-your-writes** holds for the **acting user**; other readers may see up to **≤60s** staleness only if invalidation missed—**prefer invalidation** to meet stricter correctness (PRD Measurement appendix, NFR-C1).
5. **Given** multiple app instances, **when** they share `REDIS_URL`, **then** no local-only memory cache substitutes for cross-instance contract unless documented as dev-only (NFR-C2).

## Tasks / Subtasks

- [ ] Redis module (if not from 7.4): inject `IORedis` client; connection error handling at bootstrap.
- [ ] Cache helper: `getOrSet`, `delPattern` with documented key families (`user:{id}:dashboard`, `user:{id}:invoices:list:*`).
- [ ] Hook invalidation from `InvoicesService` mutations and status transitions—**centralize** to avoid misses.
- [ ] Wire **dashboard** and **invoice list** GET handlers to cache-aside for stable-band reads.
- [ ] Document in README: endpoint classes, TTLs (if any), invalidation map (FR43).
- [ ] Tests: write then read same user sees fresh totals; second user/instance simulation with two Nest instances optional—integration with single Redis.

## Dev Notes

- **Do not** store **PDF binaries** in Redis (architecture).
- **Epic 6:** Dashboard endpoints from 6.3/6.4 should call through cache layer or service that respects invalidation—refactor if 6.x landed first.

### Technical Requirements

- Mutex or **cache stampede** protection optional for MVP; document if skipped.

### Architecture Compliance

- [Source: `architecture.md` — **Invoice and list caching** table, full section lines 207–220]

### File Structure (guidance)

- `backend/src/common/cache/cache.service.ts` or `src/modules/redis/`
- Instrument cache hit/miss metrics for 8.2 (optional counter).

### Testing Requirements

- Integration: mutate invoice → assert Redis keys deleted; GET repopulates.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 8.3
- `_bmad-output/planning-artifacts/prd.md` — FR43, NFR-C1, NFR-C2

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
