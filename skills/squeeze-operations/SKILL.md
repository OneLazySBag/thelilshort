---
name: squeeze-operations
description: Diagnose SqueezeAlert health, missing alerts, delivery errors, and recovery using redacted evidence and the runbook.
user-invocable: true
---

# SqueezeAlert operations

Paths are installation placeholders, not skill-directory-relative paths. Resolve `{{REPO_ROOT}}` and `{{WORKSPACE_ROOT}}` to their distinct absolute locations using {{REPO_ROOT}}/OPENCLAW_SETUP.md before installation. Run application commands in {{REPO_ROOT}}; keep private memory and diary in {{WORKSPACE_ROOT}}. Never copy runtime notes into repository templates.

Read {{REPO_ROOT}}/RUNBOOK.md and {{REPO_ROOT}}/docs/CONTRACTS.md. Determine whether an actual running service exists; do not run proposed commands as if already implemented.

1. Inspect health, worker/job progress, database status, and the delivery queue without exposing secrets.
2. For a ticker, check identity and coverage, evidence freshness, last market event, evaluation reason, cooldown, and intent outcome in that order.
3. Separate expected silence from missing data, stale evidence, transport failure, or code defects.
4. Propose or perform a scoped reversible repair when authorized. Preserve evidence and recovery history.
5. Recheck the affected behavior and report the actual result.

Do not clear cooldowns, lower thresholds, replay expired alerts, or enable live delivery as a generic repair. A diagnosis request does not itself authorize a real Telegram test message or deployment change. Use dry-run to reproduce whenever possible.

Output: observed status, likely cause with evidence, action taken or concrete required input, and verification outcome.
