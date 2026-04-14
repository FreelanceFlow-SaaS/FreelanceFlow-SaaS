# Story 7.4: Async email queue and rate limiting hooks

Status: ready-for-dev

## Story

As a platform operator,
I want outbound invoice email to go through a queue with sensible rate limits,
So that API latency stays predictable and abuse is reduced.

## Acceptance Criteria

1. **Given** `REDIS_URL` configured, **when** a user requests invoice email send, **then** a **BullMQ** (or equivalent) job is enqueued and the HTTP handler returns quickly with **job id** or **202 Accepted** pattern—**document** API contract (architecture, NFR-I2).
2. **Given** worker process, **when** job runs, **then** it uses **environment-only** credentials for SMTP or transactional API—never logged (NFR-I2, NFR-S2).
3. **Given** sensitive routes (`POST .../send-email`, `POST .../login`, PDF trigger per architecture), **when** traffic exceeds limits, **then** **per-`userId` and route** throttling returns **429** with stable error body—at minimum **login** and **send email** and **PDF** covered as per architecture table (architecture).
4. **Given** Redis unavailable in staging, **when** app starts, **then** behaviour is documented: fail fast vs degrade—**choose** and update README/runbook (NFR-G2).

## Tasks / Subtasks

- [ ] Add Redis + BullMQ modules; register queue and worker in Nest (separate entry or same process with `onModuleInit` consumer—document for deploy).
- [ ] Implement `@nestjs/throttler` or custom Redis sliding window keyed by `userId` + route.
- [ ] `.env.example`: `REDIS_URL`, mail provider keys, queue concurrency.
- [ ] docker-compose: Redis service for local dev; CI matrix optional Redis service for integration tests.
- [ ] Document worker scaling (horizontally safe with BullMQ).

## Dev Notes

- **Depends on:** Story 7.3 for meaningful jobs; can land **before** 7.3 with no-op processor then swap.
- **Multi-instance:** Rate limit and queue must use **shared Redis** (NFR-C2 pattern for shared state).

### Technical Requirements

- Exponential backoff for retries aligned with 7.3 (single retry for transient class).

### Architecture Compliance

- [Source: `architecture.md` — Redis: caching, rate limiting, and job queues; Implementation sequence step 3 & 5]

### File Structure (guidance)

- `backend/src/modules/queue/queue.module.ts`
- `backend/src/modules/redis/redis.module.ts`
- `backend/src/common/guards/` or throttler global config

### Testing Requirements

- Testcontainers Redis or mock ioredis for enqueue; assert 429 after N rapid calls.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 7.4
- `_bmad-output/planning-artifacts/prd.md` — NFR-I2

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
