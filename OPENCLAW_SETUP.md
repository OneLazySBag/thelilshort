# OpenClaw setup and handoff

This repository is a **design pack**, not an application or the active agent workspace. Keep the existing dedicated agent workspace separate, preserve its agent ID/display identity, and install adapted project instructions there. Do not repoint the workspace to this repository or rely on symlinks.

## Placement and bindings

```text
<repository-root>/                 # versioned architecture, specs, prompts, skill sources, templates
    AGENTS.md                     # project instruction template
    docs/CONFIGURATION.md         # sanitized defaults, no .env file
    skills/<skill-name>/SKILL.md
    templates/workspace/          # unpopulated memory/diary seeds
<private-agent-workspace>/         # existing workspace, never the GitHub publication source
    AGENTS.md, SOUL.md, IDENTITY.md, USER.md
    TOOLS.md, SKILLS.md, DREAMING.md, HEARTBEAT.md
    MEMORY.md, DREAMS.md, memory/   # private runtime only
    skills/                       # actual runtime-installed skills
<private-backup-directory>/        # originals, hashes, manifest, restore instructions
```

For installation, bind `{{REPO_ROOT}}` to the actual absolute repository path and `{{WORKSPACE_ROOT}}` to the distinct configured workspace. Bind `{{AGENT_ID}}`, `{{AGENT_DISPLAY_NAME}}`, and `{{AGENT_EMOJI}}` from inspected existing agent identity. These are template tokens, not environment variables or literal paths to leave in installed files.

Application commands always run from the repository root. Repository specs, tasks, decisions, roadmap, configuration documentation, and prompt references resolve there. Installed persona/instruction references, private MEMORY.md, DREAMS.md, and dated notes resolve to the workspace. Render **all** relative Markdown links and bare file references in installed instructions/skills to the correct absolute paths. Do not assume an installed skill is still two directories below the application repository.

## Startup loading compatibility

Startup loading depends on the runtime and harness. Compatibility checks have observed persona files (SOUL.md, IDENTITY.md, USER.md) injected while native AGENTS.md loading remains unverified. File presence or a native loading report alone does not establish that the agent received the project instructions.

The installed SOUL.md therefore directs the agent to explicitly read `{{WORKSPACE_ROOT}}/AGENTS.md` at the start of each new session, before project work, and follow its repository/workspace routing. Resolve that workspace token to an absolute path during installation. Verify the bootstrap read in a fresh session; do not assume every harness injects the same files.

TOOLS.md, SKILLS.md, DREAMING.md, and HEARTBEAT.md are explicit references to read when relevant, not assumed autoloaded context. SKILLS.md does not register skills; DREAMING.md does not activate native dreaming; HEARTBEAT.md remains comment-only and inactive.

## Adoption steps

1. Inspect the installed runtime (`openclaw --version`, `openclaw agents list --json`, `openclaw skills install --help`) and identify the existing project agent/workspace. Do not change another agent, global defaults, channels, credentials, schedules, or identity.
2. Before replacing or relocating files, back up the supplied source pack and any affected workspace files outside Git. Record SHA-256 hashes, provenance, intended adaptations, absent-versus-existing files, and exact restoration steps in a private manifest. Preserve unrelated workspace files, skills, memory, and diary entries.
3. Read README.md, AGENTS.md, ARCHITECTURE.md, TASKS.md, DECISIONS.md, docs/TIER_RULES.md, docs/CONTRACTS.md, and all supplied OpenClaw instructions/skills/prompts. Product rules remain unchanged by path compatibility work.
4. Install adapted copies of AGENTS.md, SOUL.md, IDENTITY.md, USER.md, TOOLS.md, SKILLS.md, DREAMING.md, and HEARTBEAT.md into the existing workspace. Preserve the real display name/agent ID/emoji; the supplied SqueezeAlert identity is a project role, not a rename. Keep HEARTBEAT.md comment-only and inactive. SKILLS.md remains a reference; DREAMING.md is manual guidance only.
5. Keep runtime memory and diary workspace-only. When absent, initialize from a sanitized seed in templates/workspace/ or privately preserved handoff material; otherwise read and merge deliberately in the main private session. Do not overwrite existing memory. Never copy workspace records back into public templates. Store detailed machine/runtime checks only outside Git.
6. Adapt each repository-owned skill in a temporary bundle outside the repository. Resolve both root tokens and every file reference, retaining its frontmatter and original product procedure. Install each bundle through the supported runtime CLI; do not directly edit Workshop-owned installed files. Inspect the target first; use a targeted overwrite only for a matching authorized project skill, never remove unrelated skills.

Illustrative commands after setting your private shell variables to inspected absolute locations and the existing agent ID (no secrets):

```bash
openclaw skills install --agent "$SQUEEZE_AGENT_ID" "$SQUEEZE_BUNDLE_ROOT/squeeze-build"
openclaw skills install --agent "$SQUEEZE_AGENT_ID" "$SQUEEZE_BUNDLE_ROOT/squeeze-integrations"
openclaw skills install --agent "$SQUEEZE_AGENT_ID" "$SQUEEZE_BUNDLE_ROOT/squeeze-review"
openclaw skills install --agent "$SQUEEZE_AGENT_ID" "$SQUEEZE_BUNDLE_ROOT/squeeze-operations"
openclaw skills install --agent "$SQUEEZE_AGENT_ID" "$SQUEEZE_BUNDLE_ROOT/squeeze-memory"
openclaw skills check --agent "$SQUEEZE_AGENT_ID" --json
openclaw skills list --agent "$SQUEEZE_AGENT_ID" --json
```

7. Verify installed file hashes and resolved paths, exact skill discovery, preserved identity/workspace, unchanged unrelated skills, and the absence of new recurring work. All five skills should be eligible/user-invocable. `squeeze-memory` intentionally has `disable-model-invocation: true`: it is discoverable and manually invocable, but not model-auto-selectable. Record actual output and any incompatibility privately; a Markdown file alone is not proof of runtime discovery.
8. Start a fresh private project session with prompts/01-INITIALIZE.md to verify the installed context, including the SOUL.md-directed AGENTS.md read and its repository/workspace routing. Distinguish injected files from explicit reads; record missing or unverified loading privately. Application tasks remain unchecked. Use prompts/02-BUILD.md only when requesting the local fixture-first MVP. Follow with the review prompt; invoke the reflection prompt manually when desired.

## File conventions and privacy

| Files | Role |
| --- | --- |
| Root AGENTS.md, SOUL.md, IDENTITY.md, USER.md | Versioned templates; verify adapted workspace loading; SOUL.md bootstraps an explicit AGENTS.md read |
| TOOLS.md, SKILLS.md | Explicit project references; SKILLS.md does not register a skill |
| skills/*/SKILL.md | Repository-owned skill sources, installed through the runtime CLI |
| templates/workspace/ | Sanitized seeds only; never synchronized with runtime memory |
| Workspace MEMORY.md, memory/YYYY-MM-DD.md, DREAMS.md | Private durable memory, daily notes, and diary; never published |
| DREAMING.md | Manual reflection procedure; does not configure native dreaming |
| HEARTBEAT.md | Comment-only inactive file; does not poll or schedule work |
| ROADMAP.md, docs/AGENT_ROLES.md | Shareable proposals and optional delegation briefs, not automatic registrations |

Keep credentials in local secret storage or an ignored private `.env`. Publish **no `.env` files**, including examples; docs/CONFIGURATION.md contains the proposed non-secret settings instead. Exclude databases, logs, backups, auth material, native indexes, session data, local paths, and machine-specific installation records. Review the exact staged file list and contents before publication; `.gitignore` is not a substitute for that review.

These instructions describe a compatible installation procedure, not evidence of any particular installed machine. Runtime differences must be reported. Markdown does not grant tools, permissions, account access, or activation. Adoption does not build the application, call providers, send Telegram messages, run polling, create recurring jobs, or activate native dreaming. The future Python process owns application polling independently of OpenClaw.

References: [workspace](https://docs.openclaw.ai/concepts/agent-workspace), [skills](https://docs.openclaw.ai/tools/skills), [dreaming](https://docs.openclaw.ai/concepts/dreaming).
