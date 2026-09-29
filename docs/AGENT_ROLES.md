# Agent role briefs

Start with one lead agent. The following are optional delegation briefs, not automatic registrations or a requirement to run a multi-agent fleet. Use sequential work when shared files would conflict.

## Lead engineer

Owns architecture, shared types, configuration, rule semantics, transaction design, task tracking, and final integration. Reads AGENTS.md, ARCHITECTURE.md, docs/TIER_RULES.md, docs/CONTRACTS.md, and docs/ACCEPTANCE.md. The lead is the sole writer for shared contracts and decision records while parallel work is active.

Deliver: a bounded implementation plan, integrated changes, actual verification results, updated task status, and blockers. Delegate narrow work only when interfaces are sufficiently defined.

## Integration specialist

Owns assigned provider adapters, transport tests, and the TradingView producer when delegated. Reads docs/CONTRACTS.md and docs/SOURCES.md. Identifies documented provider fields, timestamps, units, entitlements, and real fixture provenance.

Deliver: adapter implementation and tests plus a field-mapping note. Report unavailable fields or permissions precisely. Do not change tier thresholds, shared interfaces, credentials, or live-notification mode without coordination with the lead's active task.

## Verification reviewer

Owns independent review of rule correctness, duplicates, timestamp boundaries, migrations, and delivery failure behavior. Reads docs/ACCEPTANCE.md and prompts/03-REVIEW.md. Can run offline checks and report defects; no production sends are implied by review.

Deliver: findings with file locations, reproducing evidence, impact, and focused fixes if fixes are in scope. Separate blockers from preferences. Report remaining manual/live checks instead of assuming success.

## Delegation envelope

Every delegation includes: current task, allowed files, required references, input/output contract, acceptance checks, and known blockers. Include the applicable product invariants directly. Do not rely on private memory being automatically inherited.

Example brief: implement the ApeWisdom adapter and its contract tests; edit only the assigned adapter/test files; use the existing SocialObservation contract; make no network calls in tests; return changed paths and test evidence. The lead handles config and shared-type changes.
