# Prompt — build the full local MVP

Build the working local SqueezeAlert V1 from this repository's architecture pack. You are the lead Python engineer. This prompt authorizes ordinary local implementation, dependency setup, and offline verification for TASKS.md phases 1–5; continue through them without repeatedly asking whether to proceed.

Use the application repository as the working directory. Keep private agent configuration and notes outside Git.

First inspect the current repository so you preserve existing work. Read AGENTS.md, ARCHITECTURE.md, docs/TIER_RULES.md, docs/CONTRACTS.md, docs/ACCEPTANCE.md, docs/CONFIGURATION.md, TASKS.md, and DECISIONS.md. A single lead agent is sufficient; if delegating, use explicit role briefs and nonoverlapping ownership from docs/AGENT_ROLES.md.

Build a Python 3.11+ package using FastAPI, SQLAlchemy/aiosqlite, Alembic, Pydantic settings, httpx, supervised asyncio loops, pytest, and Ruff. Use pyproject.toml and a lockfile. Keep application logic in the proposed src/squeeze_alert layout, simplifying only when responsibilities remain clear.

Required behavior:

- ApeWisdom is the sole social source. Fintel supplies fuel through a documented, mockable adapter. TradingView supplies market events. Telegram is the mobile notifier.
- Tier 1 never alerts. Tier 2 alerts only when SEND_TIER_2_ALERTS=true. Tier 3/4 are eligible by default, subject to freshness, policy, cooldown, and dry-run.
- Implement the pure highest-tier engine, exact draft rule boundaries, evidence IDs/reasons, metric timestamps, and expiring current state.
- Add a strict authenticated webhook with durable inbox acceptance, duplicate/conflict handling, timestamp validation, and symbol mapping.
- Atomically record evaluation, cooldown reservation, and an outbox intent. One event qualifying for Tier 4 creates only a Tier 4 intent. Retry the same intent, recover after restart, and expire obsolete work.
- Keep external calls outside database transactions and webhook acknowledgment. Run one application worker.
- Start with fixtures and TELEGRAM_DRY_RUN=true. Synthetic evidence must never cause a real send, even if configuration is incorrect.
- Add protected inspection routes, health reporting, redacted logs, and an offline demonstration covering all tiers, escalation, duplicates, cooldown, and expiry.
- Supply a TradingView indicator and setup documentation matching the event/metric contract. Clearly report any Pine compiler or live chart checks you cannot perform.

When a Fintel endpoint, account entitlement, or real schema is unverified, finish the fixture/import adapter and record the exact missing dependency. Do not fabricate access, bypass restrictions, or silently substitute another data source. Complete every independent local task while reporting those external limits.

Do not add trade execution, broker connections, direct Reddit polling, a dashboard, predictive exhaustion, or autonomous threshold tuning. The draft thresholds are unvalidated screening rules; do not describe them as measured accuracy or high-confidence probabilities.

Run the relevant acceptance checks and configured lint. Use temporary local databases and mocked HTTP; no real Telegram sends or public deployment is part of this prompt. Correct failures in scope. Update README and RUNBOOK so their commands match what actually works.

Finish with: implemented behavior; commands/checks actually run and results; a reproducible local demo command; remaining live/manual gates; updated TASKS.md; and a concise evidence-backed implementation report. Do not mark phase 6 complete. Do not create commits or publish a repository unless separately requested.
