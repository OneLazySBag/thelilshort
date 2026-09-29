# Skill index

> Versioned project instruction template. Install an adapted copy in the separate private agent workspace; see [OPENCLAW_SETUP.md](OPENCLAW_SETUP.md). Never copy private runtime instructions or notes back into this repository.

This file is an index, not a skill registration mechanism. Repository-owned OpenClaw skill sources live in the following directories, each with YAML `name` and `description` frontmatter. [OpenClaw skill format](https://docs.openclaw.ai/tools/skills)

| Skill | Use when | File |
| --- | --- | --- |
| `squeeze-build` | Implementing an MVP milestone | [Build](skills/squeeze-build/SKILL.md) |
| `squeeze-integrations` | Building or diagnosing provider/webhook adapters | [Integrations](skills/squeeze-integrations/SKILL.md) |
| `squeeze-review` | Reviewing correctness and running acceptance checks | [Review](skills/squeeze-review/SKILL.md) |
| `squeeze-operations` | Inspecting runtime health or delivery failures | [Operations](skills/squeeze-operations/SKILL.md) |
| `squeeze-memory` | Summarizing verified work and maintaining project memory | [Memory](skills/squeeze-memory/SKILL.md) |

These are repository-owned engineering skill sources. Install adapted bundles with the installed runtime CLI as described in OPENCLAW_SETUP.md; listing this file alone does not install them. `squeeze-memory` is deliberately manual-only (`disable-model-invocation: true`): it should be discoverable and user-invocable, but absent from the model auto-selection catalog.

These skills do not provision tools, enable live credentials, create persistent agents, start jobs, or send notifications merely by being present.
