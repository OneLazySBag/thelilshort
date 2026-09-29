# Prompt — adopt the SqueezeAlert design pack

Adopt this SqueezeAlert V1 architecture pack for the existing project agent, keeping the application repository and private OpenClaw workspace distinct. Inspect both locations, the current agent identity, and the installed OpenClaw version before changing files. Preserve the existing agent ID/display name/emoji and workspace; never repoint the workspace to this repository or use symlinks. This pack is project context, not evidence of a built application.

Read repository README.md, AGENTS.md, SOUL.md, IDENTITY.md, USER.md, TOOLS.md, ARCHITECTURE.md, docs/TIER_RULES.md, docs/CONTRACTS.md, TASKS.md, DECISIONS.md, SKILLS.md, DREAMING.md, HEARTBEAT.md, and OPENCLAW_SETUP.md, plus all supplied skills and prompts. In the main private session only, read existing workspace MEMORY.md and relevant recent dated notes if present; never read private memory into a shared conversation or subagent.

Follow OPENCLAW_SETUP.md: back up originals outside Git; record hashes, provenance, adaptations, and restoration steps; install the eight authorized workspace instruction files with every path resolved to the correct absolute repository/workspace location; preserve unrelated files and skills. Install all five adapted project skills from temporary local bundles using the supported `openclaw skills install --agent <existing-agent-id> <local-dir>` CLI, not direct edits to Workshop-owned files.

Verify exact discovery with `openclaw skills check --agent <existing-agent-id> --json` and `openclaw skills list --agent <existing-agent-id> --json`. Keep `squeeze-memory` manual-only: discovery/user invocation are expected, model auto-selection is intentionally disabled. SKILLS.md is only an index. DREAMING.md is manual guidance; HEARTBEAT.md stays comment-only/inactive. Do not activate native dreaming or create schedules.

Keep private MEMORY.md, DREAMS.md, dated notes, and detailed adoption records only in the workspace or another private location outside Git. Repository instruction files and templates are sanitized shareable source, never round-tripped from runtime copies. Exclude all environment files (including examples), DBs, logs, backups, auth records, local paths, and machine-specific notes. Proposed defaults live in docs/CONFIGURATION.md.

Record actual installed-runtime and file/skill checks privately. Report changed files, backup/manifest location, preserved identity/workspace, discovery results, and any blocker. Keep application tasks unchecked. No application build, provider calls, Telegram sends, polling, public deployment, or recurring jobs are part of adoption. Publication is performed only if separately requested; review exact staged contents first.

Next concrete action after adoption is an explicit request using prompts/02-BUILD.md for the local fixture-first MVP. Do not claim tests, integrations, or native dreaming have run before they exist.
