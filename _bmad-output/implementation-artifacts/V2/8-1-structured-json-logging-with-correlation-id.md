# Story 8.1: Structured JSON logging with correlation id

Status: ready-for-dev

## Story

As an operator,
I want JSON logs with severity, timestamp, service name, and correlation id per request,
So that I can troubleshoot in Loki or similar tools without leaking secrets or full invoice payloads.

## Acceptance Criteria

1. **Given** an HTTP request completes, **when** logs are written, **then** each line is **parseable JSON** including at minimum **timestamp**, **severity**, **service name**, **correlation id** (`requestId`), **route**, **HTTP status** (NFR-O1, PRD Measurement appendix for field list).
2. **Given** logs from API and **background workers** (email jobs), **when** inspected, **then** they use the **same field vocabulary** where applicable (job id as correlation extension).
3. **Given** secrets, JWT, passwords, provider API keys, **when** any middleware logs errors, **then** they are **not** present in plaintext (NFR-O1, NFR-S2).
4. **Given** invoice bodies, **when** logging request failures, **then** **no full line items or totals** are dumped—at most `invoiceId` if needed for support (architecture PII rules).

## Tasks / Subtasks

- [ ] Replace default Nest logger with **pino** or **nestjs-pino** (team choice) emitting one JSON object per line.
- [ ] Add `AsyncLocalStorage` or middleware to generate/propagate `requestId` (UUID); attach to response header `X-Request-Id` for traceability.
- [ ] Redact sensitive keys in serializer (`password`, `authorization`, `cookie`, email bodies).
- [ ] Document log field schema version in `README` or `docs/runbook-logging.md`.
- [ ] Optional: OpenTelemetry trace id correlated with `requestId` for Story 8.2 stack—wire if low effort.

## Dev Notes

- **Minimize raw email** in mailer logs (architecture).
- **GDPR:** userId logging policy—hash or omit in production if required—**document team choice**.

### Technical Requirements

- Log level from `LOG_LEVEL` env; JSON in production, pretty in local optional.

### Architecture Compliance

- [Source: `architecture.md` — Observability LGTM; structured JSON for Loki]

### File Structure (guidance)

- `backend/src/main.ts` — logger bootstrap
- `backend/src/common/middleware/request-id.middleware.ts`
- `backend/src/common/interceptors/logging.interceptor.ts` (optional)

### Testing Requirements

- Unit: redaction function strips known secret patterns.
- Snapshot one log line shape from e2e.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 8.1
- `_bmad-output/planning-artifacts/prd.md` — NFR-O1; Measurement (structured logs)

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
