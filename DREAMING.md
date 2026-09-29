# Project reflection and memory curation

> Versioned project instruction template. Install an adapted copy in the separate private agent workspace; see [OPENCLAW_SETUP.md](OPENCLAW_SETUP.md). Never copy private runtime instructions or notes back into this repository.

This file is a project procedure. Its name does not register an OpenClaw feature, configure the native dreaming engine, or create a schedule. Future product ideas belong in ROADMAP.md.

Current OpenClaw documents native dreaming as memory consolidation, with human-readable output in {{WORKSPACE_ROOT}}/DREAMS.md and durable promotion into {{WORKSPACE_ROOT}}/MEMORY.md. Consult the installed release before configuring it. [OpenClaw dreaming](https://docs.openclaw.ai/concepts/dreaming)

## Manual project reflection

Use the manual-only squeeze-memory skill or prompts/04-REFLECT.md only for an explicitly requested curation pass in a main private session. Read recent dated notes under {{WORKSPACE_ROOT}}/memory/, TASKS.md, DECISIONS.md, relevant diffs, and actual check output.

1. Identify verified facts, unresolved questions, repeated implementation problems, and useful next actions.
2. Remove duplicate proposed entries; distinguish user requirements from engineering assumptions.
3. Promote a concise durable fact only when it has a source and remains true beyond one session. Preserve corrections and superseded decisions.
4. Append a dated summary to {{WORKSPACE_ROOT}}/DREAMS.md stating inputs, promoted facts, rejected hypotheses, and proposals.
5. Put future feature ideas in ROADMAP.md with status `proposal`; do not silently make them active tasks.

Do not infer a validated edge from a few alerts. Store individual ticker observations in SQLite, not permanent memory. Do not alter application thresholds, code, credentials, notification settings, or deployment during a memory-only reflection.

## Native dreaming compatibility

Markdown guidance does not technically constrain all native memory-engine writes. The canonical requirements remain in AGENTS.md, DECISIONS.md, and docs/TIER_RULES.md even if {{WORKSPACE_ROOT}}/MEMORY.md is rewritten. Review native promotions against those sources.

Choose one memory-writing workflow at a time for this workspace to avoid conflicting edits. A manual reflection can run without native dreaming. If native dreaming is already enabled, inspect its state and avoid a parallel custom schedule. If native dreaming is absent on the installed version, continue with the manual prompt; do not invent configuration keys.

No recurring reflection or OpenClaw setting was enabled by producing this pack. Any later scheduling should specify cadence, cost limits, write scope, and meaningful-change-only notifications. The application polling loops remain independent.
