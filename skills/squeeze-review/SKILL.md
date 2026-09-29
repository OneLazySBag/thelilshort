---
name: squeeze-review
description: Review SqueezeAlert rule correctness, event durability, deduplication, freshness, and notification behavior with evidence.
user-invocable: true
---

# SqueezeAlert verification review

Paths are installation placeholders, not skill-directory-relative paths. Resolve `{{REPO_ROOT}}` and `{{WORKSPACE_ROOT}}` to their distinct absolute locations using {{REPO_ROOT}}/OPENCLAW_SETUP.md before installation. Run application commands in {{REPO_ROOT}}; keep private memory and diary in {{WORKSPACE_ROOT}}. Never copy runtime notes into repository templates.

Read {{REPO_ROOT}}/docs/ACCEPTANCE.md, {{REPO_ROOT}}/docs/TIER_RULES.md, and {{REPO_ROOT}}/ARCHITECTURE.md. Inspect the implementation before drawing conclusions.

Trace one event from authentication to database commit, evaluation, cooldown reservation, outbox claim, Telegram response, and restart. Examine duplicate and out-of-order events, stale fuel, delayed processing, ambiguous delivery, and config changes.

Check that every tier uses required lower-tier evidence; Tier 1 cannot create an intent; default Tier 2 is silent; Tier 4 escalation does not wait for Tier 3 cooldown. Verify one highest-tier intent per trigger and fixture/live separation.

Run relevant offline checks and distinguish executed checks from inspection-only conclusions. Report defects with location, triggering case, consequence, and a minimal fix. Prioritize behavioral bugs over style. Do not certify live provider access or Pine behavior from Python fixtures.

Output: ordered findings, checks performed, unresolved manual/live checks, and readiness separately for local demo and live pilot. If no defect is found, state review coverage and limitations without inventing issues.
