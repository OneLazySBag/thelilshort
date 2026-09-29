# Development and operating runbook

The following command interface is a target for the implementation, not a claim that commands work in this documentation-only pack. Update this file to match the actual implementation before declaring the MVP complete.

Run application commands from the repository root. Read [docs/CONFIGURATION.md](docs/CONFIGURATION.md) for the proposed settings.

## Local development after implementation

```bash
uv sync --locked
# Create a private .env from docs/CONFIGURATION.md; do not copy the Markdown file.
uv run alembic upgrade head
uv run uvicorn squeeze_alert.main:app --host 127.0.0.1 --port 8000 --workers 1
```

Before startup, supply a locally generated dedicated webhook token and admin token. Keep DATA_MODE=fixture and TELEGRAM_DRY_RUN=true initially. The build's demo command should create an isolated temporary database and test-only tokens automatically, requiring no real credentials.

Proposed verification commands:

```bash
uv run pytest
uv run ruff check .
uv run python scripts/replay_demo.py
```

Do not run these against a production database. The demo and tests use a synthetic listing, a controlled clock, and mocked HTTP. Report Pine validation separately from Python tests.

## Runtime configuration

One worker, one replica, persistent data directory. Service supervisor restarts the process if required; the application resumes durable pending work and expires obsolete events. OpenClaw does not need to stay running.

The first public webhook deployment needs HTTPS on a supported endpoint, authenticated ingress, redacted logs, a bounded request body, and persistent disk. TradingView also has account/setup prerequisites described in its official documentation. Check those when configuring the actual account. [TradingView webhook setup](https://www.tradingview.com/support/solutions/43000529348-how-to-configure-webhook-alerts/)

## Investigating a missing alert

1. Check process/DB readiness and job health.
2. Inspect the ticker's canonical identity and TradingView coverage.
3. Inspect social and fuel evidence, source dates, passed branches, and availability reasons.
4. Check whether a market event was received, accepted, fresh, supported, and newer than the watermark.
5. Inspect the evaluation tier and suppression reason, then cooldown and outbox status.
6. Check dry-run/fixture mode and Telegram result without exposing credentials.

A silent Tier 1/2 result is expected behavior. Missing coverage, unavailable fuel, expired evidence, or cooldown suppression is not repaired by lowering thresholds without analysis.

## Delivery failure

Transient failures retry the same intent subject to freshness and attempt limits. Permanent token/destination failures require correcting local configuration. A queued market alert that has expired must not be forced through after repair. A fresh event can create a later alert when cooldown permits.

If a message may have succeeded but acknowledgment was lost, a duplicate is possible. Use the stable alert ID to identify it. Do not delete delivery history to conceal it.

## Backup and restart

Use SQLite's backup API or a clean service stop before copying the database. Preserve migration version, environment configuration through secure local means, and dependency lock. Restore into an isolated directory first; check database integrity and schema compatibility. Test restart recovery without enabling real delivery.

Retention is initially manual; do not add automatic deletion without a defined policy. Never clear cooldowns or replay terminal dry-run/sent rows merely because the service restarted.

## Live activation

Live operation is a separate task after the offline build. Needed inputs: verified Fintel mode and entitlements, verified TradingView producer and coverage, public endpoint, bot/destination credentials, persistent host, and the user's activation request. Until then, complete local implementation and report external dependencies precisely.
