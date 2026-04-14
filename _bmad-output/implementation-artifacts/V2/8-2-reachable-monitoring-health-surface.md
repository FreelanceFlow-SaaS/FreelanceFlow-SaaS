# Story 8.2: Reachable monitoring / health surface

Status: ready-for-dev

## Story

As an operator,
I want staging and production to expose a clear path to health, errors, and basic metrics,
So that I can verify liveness quickly during incidents.

## Acceptance Criteria

1. **Given** staging and production URLs documented in **README**, **when** I follow links (or embedded admin page), **then** I can verify **service liveness** (e.g. `/health` or `/api/v1/health` returns 200 with version/build id) (NFR-O2).
2. **Given** NFR-O2 minimum, **when** I open the monitoring surface, **then** I see **recent errors or error rate** placeholder—acceptable: link to **Grafana Cloud** / **Prometheus** / **Render metrics** with screenshot or one-click path—**no mandated vendor** (NFR-O2).
3. **Given** optional metrics (request rate, queue depth), **when** enabled, **then** they appear or link out—document which are **phase 1** vs future.
4. **Given** Redis and mail worker in V2, **when** health check runs, **then** dependency status is **degraded** (503 or body flag) if Redis required for critical path is down—**document** semantics (NFR-G2).

## Tasks / Subtasks

- [ ] Implement `HealthModule` with `@nestjs/terminus`: DB ping, optional Redis ping.
- [ ] Expose build `GIT_SHA` or `npm_package_version` via env at build time for FR31 alignment.
- [ ] README section: “Operations” with links to Grafana dashboards (Loki for logs, Prometheus for metrics) as stubs if not provisioned yet—**must** be non-empty paths for evaluators.
- [ ] Optional: minimal Next **internal** route `/ops` protected by env flag or IP allowlist—**document security**; many teams use README-only—either satisfies NFR-O2 if README is explicit.

## Dev Notes

- **Do not** bundle heavy Grafana JS SDK in customer-facing app without auth story—prefer **external links**.

### Technical Requirements

- Health endpoint must be **fast** (<100ms) and safe under load—no heavy queries.

### Architecture Compliance

- [Source: `architecture.md` — Observability Grafana stack; no Elastic default]

### File Structure (guidance)

- `backend/src/modules/health/health.controller.ts`

### Testing Requirements

- E2e: `/health` returns 200 with DB up; returns 503 when DB mocked down if such test is feasible.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 8.2
- `_bmad-output/planning-artifacts/prd.md` — NFR-O2
- `docs/v2.md` — monitoring

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
