# Agent role briefs

Start with one lead agent using the project skills. The following are optional delegation briefs, not automatic registrations or a requirement to run a multi-agent fleet. Use sequential work when shared files would conflict.

## Lead engineer

Owns architecture, shared types, configuration, rule semantics, transaction design, task tracking, and final integration. Reads AGENTS.md, ARCHITECTURE.md, TIER_RULES.md, CONTRACTS.md, and ACCEPTANCE.md. The lead is the sole writer for shared contracts and decision records while parallel work is active.

Deliver: a bounded implementation plan, integrated changes, actual verification results, updated task status, and blockers. Delegate narrow work only when interfaces are sufficiently defined.

## Integration specialist

Owns assigned provider adapters, transport tests, and the TradingView producer when delegated. Reads the integration skill and contracts. Identifies documented provider fields, timestamps, units, entitlements, and real fixture provenance.

Deliver: adapter implementation and tests plus a field-mapping note. Report unavailable fields or permissions precisely. Do not change tier thresholds, shared interfaces, credentials, or live-notification mode without coordination with the lead's active task.

## Verification reviewer

Owns independent review of rule correctness, duplicates, timestamp boundaries, migrations, and delivery failure behavior. Reads ACCEPTANCE.md and the review skill. Can run offline checks and report defects; no production sends are implied by review.

Deliver: findings with file locations, reproducing evidence, impact, and focused fixes if fixes are in scope. Separate blockers from preferences. Report remaining manual/live checks instead of assuming success.

## Memory curator

Owns requested curation of private workspace MEMORY.md, dated notes, and DREAMS.md only in the main private session, not by loading private memory into a subagent. Reads the installed workspace DREAMING.md and the manual-only memory skill. Repository specs/tasks/roadmap remain in the separate application repository. Never copy runtime memory into repository templates. May propose roadmap changes but cannot convert them into active requirements or change runtime behavior.

Deliver: sourced durable facts, corrections, clearly marked proposals, and a compact next action. Do not infer signal validity, financial performance, or user authorization from old notes.

## Delegation envelope

Every delegation includes: current task, allowed files, required references, input/output contract, acceptance checks, and known blockers. Include the applicable product invariants directly. Do not rely on private memory being automatically inherited.

Example brief: implement the ApeWisdom adapter and its contract tests; edit only the assigned adapter/test files; use the existing SocialObservation contract; make no network calls in tests; return changed paths and test evidence. The lead handles config and shared-type changes.
