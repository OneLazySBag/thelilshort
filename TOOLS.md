# Environment reference

> Versioned project instruction template. Install an adapted copy in the separate private agent workspace; see [OPENCLAW_SETUP.md](OPENCLAW_SETUP.md). Never copy private runtime instructions or notes back into this repository.

This is a project reference explicitly read through AGENTS.md. Do not assume this filename is automatically loaded by every OpenClaw release.

## Development

Proposed runtime: Python 3.12 with uv, FastAPI, SQLAlchemy/aiosqlite, Alembic, httpx, pytest, and Ruff. Application commands in RUNBOOK.md become available only after implementation. Current pack contains no runnable application.

Use {{REPO_ROOT}} as the application working directory. Keep the OpenClaw workspace at {{WORKSPACE_ROOT}}, separate from the repository. Inspect the installed OpenClaw release and existing agent configuration before proposing changes; preserve the current agent ID, identity, and workspace.

## Configuration

Application environment variables are listed in docs/CONFIGURATION.md. Configuration loads and validates them once at startup. Python config definitions and executable tests become authoritative for defaults after implementation; keep this template synchronized.

`DATA_MODE=fixture` means no external source traffic. `DATA_MODE=live` permits verified source adapters. `FINTEL_MODE` selects fixture, authorized local import, or verified live API independently; any fixture evidence still blocks real notifications. `TELEGRAM_DRY_RUN=true` is independent of data mode.

Startup must reject fixture data combined with requested real delivery, missing/placeholder webhook credentials, invalid threshold ordering, nonpositive polling/TTL limits, and unsupported modes. A local demo helper may generate ephemeral test-only credentials without printing them. Telegram credentials are required only for live delivery. Fintel live mode requires a verified contract and entitlement, not just a nonempty key.

Use separate local databases for offline demos and live operation. Configure the Telegram destination locally; do not copy it into memory. Inspection uses a separate admin token. Never display .env contents or full provider URLs containing credentials.

## Operations

Single process/worker only. Keep persistent application data under {{REPO_ROOT}}/data/. Use SQLite-aware backup rather than copying a live main database file without its WAL state. HTTP retries are bounded. No OpenClaw timer should duplicate the application's polling loops.

OpenClaw credentials, model configuration, session data, and native memory indexes remain outside the application repository. The published design pack makes no machine-specific installation claim; retain actual runtime checks only in the private workspace or local adoption manifest.
