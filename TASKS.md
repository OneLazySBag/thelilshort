# Implementation tasks

Status: design pack delivered; all application tasks below are open. Complete checkboxes only after evidence exists. The fixture-first build prompt authorizes phases 1–5 as a local MVP; phase 6 depends on external access and activation details.

## Phase 1 — runnable local foundation

- [ ] Create pyproject.toml, package layout, dependency lock, and local setup commands.
- [ ] Add validated settings, application factory, structured redacted logging, and health routes.
- [ ] Add SQLAlchemy models and an initial Alembic migration with documented constraints.
- [ ] Add lifespan-managed HTTP/DB resources and supervised single-worker loops.

Exit evidence: clean local startup, schema migration from empty DB, health response, fixture-only configuration with no external calls.

## Phase 2 — deterministic rules

- [ ] Implement typed observations, source timestamps, and fixture provenance.
- [ ] Implement pure tier evaluation and independent notification policy.
- [ ] Add boundary tests for all tiers, missing metrics, age limits, OR-branch expiry, and source ordering.
- [ ] Implement current-state expiry and historical decision recording.

Exit evidence: focused unit checks pass against docs/TIER_RULES.md with an injected clock.

## Phase 3 — durable event and notification pipeline

- [ ] Implement webhook authentication, strict validation, symbol resolution, and durable acknowledgment.
- [ ] Implement duplicate/conflict handling and ordered event processing.
- [ ] Atomically record evaluations, cooldown reservations, and outbox intents.
- [ ] Implement dry-run delivery, retry states, leases, expiry, and restart recovery.

Exit evidence: duplicate/concurrency/restart integration tests pass using a temporary SQLite file; no real messages.

## Phase 4 — provider and producer boundaries

- [ ] Implement ApeWisdom adapter with HTTP mocks and error/partial-page handling.
- [ ] Implement Fintel fixtures and authorized-import contract; clearly gate unfinished live access.
- [ ] Implement Telegram client behind dry-run and fixture guards, tested with mocked HTTP.
- [ ] Add a TradingView indicator implementing the specified metrics and payload contract.
- [ ] Document symbol coverage, indicator setup, producer versioning, and manual Pine verification still needed.

Exit evidence: adapter contract checks pass; unsupported live integrations are explicitly reported, not simulated as success. Pine verification may remain a named external/manual gate while the local Python MVP is complete.

## Phase 5 — reviewable local MVP

- [ ] Add protected inspection endpoints and the offline end-to-end demo.
- [ ] Run relevant unit/integration/contract tests and configured lint checks.
- [ ] Exercise database migration and pending-work restart recovery.
- [ ] Update README/RUNBOOK with commands actually implemented.
- [ ] Run the review prompt and resolve material findings.
- [ ] Record completion evidence, limitations, and next steps in the repository task/decision documentation.

Exit evidence: reproducible fixture demo for tiers, duplicate, cooldown, escalation, and expiry; clean checks; no claimed live readiness.

## Phase 6 — live pilot, separate activation

- [ ] Confirm Fintel endpoint entitlement, schema, dates, units, quotas, and authorized use.
- [ ] Verify TradingView indicator compilation, metrics, feed status, and alert coverage.
- [ ] Configure local secrets, public HTTPS ingress, and persistent host storage.
- [ ] Verify the Telegram destination with an explicitly scoped test message.
- [ ] Run live-data dry-run observation and review evidence quality.
- [ ] Enable live delivery only under the user's activation request, then verify one controlled alert.

Optional after a demonstrated need: container packaging, dashboard, or other ROADMAP.md work.
