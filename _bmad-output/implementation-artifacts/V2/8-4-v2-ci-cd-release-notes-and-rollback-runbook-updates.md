# Story 8.4: V2 CI/CD, release notes, and rollback/runbook updates

Status: ready-for-dev

## Story

As a team member,
I want CI/CD to stay green and V2 operations documented with tags and rollback guidance,
So that staging and production remain reachable and evaluators can trace releases (NFR-O3, NFR-G1, NFR-G2).

## Acceptance Criteria

1. **Given** V2 changes (Redis, mail, workers, new env vars), **when** CI runs on the **default branch**, **then** existing stages (**install → lint → format → test → build → Docker** as applicable per repo) **pass** or waivers are **explicitly documented** with owner approval (NFR-O3).
2. **Given** release time, **when** V2 ships, **then** there is a **git tag** (recommended **`v2.0.0`** or documented scheme) and **release notes** listing operational changes: **email provider**, **Redis**, **queue workers**, **monitoring links**, new **env vars** (NFR-G1).
3. **Given** production incident from email or cache, **when** an operator reads the runbook, **then** **rollback** or **feature-flag/disable** steps are documented (e.g. disable send-email route, disable Redis cache via flag, scale workers to zero) (NFR-G2).
4. **Given** backlog hygiene, **when** reviewing issues/PRs, **then** V2 work references **FR32+** or story keys in commit messages—**document** convention in CONTRIBUTING or README (NFR-G1).

## Tasks / Subtasks

- [ ] Update both repos’ GitHub Actions / CI configs: add Redis service to job matrix where integration tests need it; split worker image build if separate deployable.
- [ ] `CHANGELOG.md` or GitHub Release body: V2 section.
- [ ] README: env matrix table (`REDIS_URL`, `SMTP_*`, provider keys, `LOG_LEVEL`, `OTEL_*` if any).
- [ ] `docs/runbook-v2.md` or extend existing: rollback, cache flush, queue drain.
- [ ] Verify Docker Compose local full stack still starts (Epic 6 DevOps story lineage).

## Dev Notes

- **Non-code:** This story is **documentation + pipeline** heavy; still requires PR to default branch.
- **Load tests:** If staging load test is gated before prod, document whether V2 scenario added—optional subtask.

### Technical Requirements

- No reduction of test coverage without team sign-off (NFR-O3).

### Architecture Compliance

- [Source: `architecture.md` — CI/CD per repo, staging promotion]

### File Structure (guidance)

- `.github/workflows/*.yml` (paths per repo)
- `README.md` (root or per service)
- `docs/runbook-v2.md` (new)

### Testing Requirements

- CI green on PR opened from branch containing all V2 features; smoke script optional `curl /health`.

### References

- `_bmad-output/planning-artifacts/epics.md` — Story 8.4
- `_bmad-output/planning-artifacts/prd.md` — NFR-G1, NFR-G2, NFR-O3
- `docs/v2.md` — constraints (CI/CD, deployment)

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### File List

---

**Story completion status:** ready-for-dev — Ultimate context engine analysis completed - comprehensive developer guide created
