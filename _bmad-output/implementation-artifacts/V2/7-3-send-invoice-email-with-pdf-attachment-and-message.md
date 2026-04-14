# Story 7.3: Send invoice email with PDF attachment and message

Status: ready-for-dev

## Story

As an authenticated freelancer,
I want to send an invoice by email to one or more recipients with a custom message and the PDF attached,
So that clients receive the same document the app would show, without false success on failure.

## Acceptance Criteria

1. **Given** valid recipient list and message (subject/body fields as team defines), **when** I confirm send, **then** PDF bytes are built from **persisted invoice at send time** (same rules as FR24–FR27) and handed to the mail layer (FR32, FR33, architecture).
2. **Given** provider accepts the job for delivery (or SMTP handshake completes per chosen semantics), **when** the API/worker finishes successfully, **then** UI shows success toast; **do not** claim success on enqueue alone if product defines success at delivery—**document** chosen semantics (NFR-I2, FR33).
3. **Given** **transient** provider error, **when** the worker retries, **then** at most **one** automatic retry within **documented cumulative wait** (PRD default e.g. ≤ 30 s total) before user-visible failure (NFR-I2, PRD appendix).
4. **Given** **permanent** failure (e.g. invalid mailbox), **when** send completes, **then** user sees **clear French non-success** message and **no wording** implying the invoice was delivered (FR33).
5. **Given** invoice not owned or wrong `userId`, **when** send is requested, **then** 404/403 (FR4).

## Tasks / Subtasks

- [ ] DTO: `to[]`, `subject`, `body` (limits on length); validate email format array.
- [ ] Integrate with queue from Story 7.4 if merged—otherwise stub sync send behind feature flag only for dev—**prefer 7.4 first** in sprint order for staging safety.
- [ ] Generate PDF stream in worker or request scope; attach; **minimize** raw email content in logs (NFR-O1).
- [ ] Next: send dialog on invoice detail; disabled when status inappropriate if product requires (e.g. only `sent`/`paid`)—**align with PM**; default: allow from `draft` with warning or block—**document**.
- [ ] Tests: mock provider; assert retry count; assert no success on hard bounce mock.

## Dev Notes

- **Order with 7.4:** Implement **queue + worker** in 7.4; this story can focus on **mail content + DTO + UI** calling enqueue API—split as team prefers.
- **Idempotency:** Optional idempotency key per `(invoiceId, sendRequestId)` to avoid duplicate sends on double-click—**document**.

### Technical Requirements

- Attachment size under provider limit; if too large, return actionable error or offer link-only mode (architecture alternative).

### Architecture Compliance

- [Source: `architecture.md` — Email delivery, async BullMQ, SMTP_* dev / API prod]
- NFR-I2: env-managed credentials; bounded retry.

### File Structure (guidance)

- `backend/src/modules/mail/`
- `backend/src/modules/queue/processors/send-invoice-email.processor.ts`
- `frontend/src/components/features/invoices/SendInvoiceEmailDialog.tsx`

### Testing Requirements

- Integration with mailhog or ethereal in dev; contract tests with mocked transport.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 7.3
- `_bmad-output/planning-artifacts/prd.md` — FR32, FR33, NFR-I2
- `docs/v2.md` — email + PDF

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
