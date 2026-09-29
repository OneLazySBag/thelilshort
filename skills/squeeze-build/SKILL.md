---
name: squeeze-build
description: Implement a local SqueezeAlert V1 milestone with deterministic rules, durable SQLite state, and fixture-first checks.
user-invocable: true
---

# SqueezeAlert build

Paths are installation placeholders, not skill-directory-relative paths. Resolve `{{REPO_ROOT}}` and `{{WORKSPACE_ROOT}}` to their distinct absolute locations using {{REPO_ROOT}}/OPENCLAW_SETUP.md before installation. Run application commands in {{REPO_ROOT}}; keep private memory and diary in {{WORKSPACE_ROOT}}. Never copy runtime notes into repository templates.

Use when implementing or repairing the SqueezeAlert application. Read {{REPO_ROOT}}/AGENTS.md, {{REPO_ROOT}}/TASKS.md, {{REPO_ROOT}}/ARCHITECTURE.md, and the relevant contracts in {{REPO_ROOT}}/docs/.

1. Inspect existing files and current task status before generating code. Preserve working code and user changes.
2. Select the next incomplete milestone within the active request. Explain the concrete next change briefly.
3. Keep domain rules pure. Wire HTTP adapters, persistence, and notification policy through services.
4. Implement focused checks from {{REPO_ROOT}}/docs/ACCEPTANCE.md alongside behavior. Use fixtures, injected time, and a temporary SQLite file where concurrency matters.
5. Run the relevant checks, fix failures within scope, and update {{REPO_ROOT}}/TASKS.md only from actual results.
6. Report completed behavior, evidence, remaining limitations, and the next action. Append factual continuity notes under {{WORKSPACE_ROOT}}/memory/.

Tier 1 never alerts; Tier 2 is opt-in; Tier 3/4 notify by default. All credentials stay local. Fixtures never cause live sends. Do not invent Fintel access, validated thresholds, Pine compilation results, or production readiness. Complete the offline build even when live credentials are unavailable.

Output: implementation diff, actual verification results, updated task status, and a concise handoff.
