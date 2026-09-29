# Repository architecture

## Recommended shape

Use a modular monolith: one repository, one FastAPI process, one local SQLite database. Keep network adapters, rule evaluation, persistence, and notification delivery separate. Python 3.12 is the recommended baseline; maintain Python 3.11+ compatibility if practical.

Choose FastAPI, Pydantic settings, SQLAlchemy 2 with aiosqlite, Alembic migrations, httpx, pytest, and Ruff. Use supervised asyncio loops in FastAPI lifespan for this small polling workload. A separate scheduler, message broker, and worker service are unnecessary for V1. Resolve and lock compatible dependency versions during implementation.

Use `pyproject.toml` plus `uv.lock` as the dependency source of truth. Do not maintain an independent requirements list. An exported requirements file is acceptable when required by a deployment target.

## Proposed tree

Files under `src/`, `tests/`, `migrations/`, `scripts/`, and `tradingview/`, plus `pyproject.toml`, `uv.lock`, and `alembic.ini`, are planned implementation files, not included application code. See README.md for the actual design-pack tree. AGENTS.md contains repository-specific engineering instructions; private agent workspace files are outside this tree. Application commands and relative database paths resolve from the repository root.

```text
squeeze-alert-v1/
├── README.md
├── ARCHITECTURE.md
├── AGENTS.md
├── ROADMAP.md
├── TASKS.md
├── DECISIONS.md
├── RUNBOOK.md
├── .gitignore
├── pyproject.toml
├── uv.lock
├── alembic.ini
├── src/squeeze_alert/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│   ├── cli.py
│   ├── domain/
│   │   ├── types.py
│   │   ├── tier_engine.py
│   │   └── notification_policy.py
│   ├── api/
│   │   ├── schemas.py
│   │   ├── dependencies.py
│   │   └── routes/
│   │       ├── health.py
│   │       ├── tradingview.py
│   │       └── inspection.py
│   ├── adapters/
│   │   ├── contracts.py
│   │   ├── apewisdom.py
│   │   ├── fintel.py
│   │   ├── telegram.py
│   │   └── fixtures.py
│   ├── services/
│   │   ├── ingest.py
│   │   ├── evaluate.py
│   │   ├── delivery.py
│   │   └── symbol_map.py
│   ├── storage/
│   │   ├── database.py
│   │   ├── models.py
│   │   └── repositories.py
│   ├── jobs/
│   │   ├── supervisor.py
│   │   ├── poll_social.py
│   │   ├── poll_fuel.py
│   │   ├── process_events.py
│   │   ├── deliver_alerts.py
│   │   └── expire_state.py
│   └── observability.py
├── migrations/versions/
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── contracts/
│   └── fixtures/
├── scripts/
│   ├── replay_demo.py
│   └── smoke_webhook.py
├── tradingview/
│   ├── squeeze_alert.pine
│   └── README.md
├── docs/
│   ├── TIER_RULES.md
│   ├── CONTRACTS.md
│   ├── ACCEPTANCE.md
│   ├── AGENT_ROLES.md
│   ├── SOURCES.md
│   └── CONFIGURATION.md
├── prompts/
│   ├── 02-BUILD.md
│   └── 03-REVIEW.md
└── data/                           # runtime only, gitignored
```

Add package `__init__.py` files as appropriate. Add container files only after the local acceptance suite passes.

## Boundaries

| Area | Responsibility | Must not do |
| --- | --- | --- |
| `domain/` | Pure functions: observations + time + configuration → tier, reasons, expiry, notification eligibility | Import HTTP, FastAPI, ORM, or an LLM |
| `adapters/` | HTTP transport, source parsing, units, provider errors | Decide tiers or call another provider implicitly |
| `services/` | Coordinate records, evaluation, transactions, rendering | Hide network requests inside a database transaction |
| `storage/` | Tables, constraints, queries, atomic reservations | Make financial judgments |
| `api/` | Authenticate, validate, persist inbound events, expose authorized inspection | Wait for Fintel or Telegram |
| `jobs/` | Poll and drain durable work, apply retry timing, supervise health | Keep the only copy of pending work in RAM |

`domain` is independent. Adapters and storage implement interfaces consumed by services. API and jobs call services. `main.py` wires dependencies. Prefer concrete repositories over a generic repository framework.

## Execution flow

1. The social loop fetches ApeWisdom, validates stock symbols, records observations, and evaluates Tier 1. Initially poll every five minutes, subject to provider limits.
2. The fuel loop selects fresh Tier 1 candidates and obtains Fintel observations, subject to caching, entitlements, and rate limits. Initially poll eligible symbols every fifteen minutes. It can establish Tier 2.
3. TradingView posts an authenticated, timestamped event. The route validates and stores it in the SQLite inbox, commits, and returns `202`. An exact duplicate returns `200`.
4. The event worker processes accepted market events in receive order. In one short transaction it loads eligible prior evidence, evaluates the highest tier, records reasons, updates state, reserves a cooldown, and inserts an alert intent when eligible.
5. The delivery worker claims an intent, rechecks freshness and policy, sends outside the transaction, and records the outcome.
6. An expiry loop recomputes active state without generating new market alerts. An inspection request also evaluates freshness at read time.

Use one evaluation writer at a time within the process and short SQLite write transactions. Guard reservations with database constraints and transactional updates even though V1 has one worker. Do not rely solely on a Python set or mutex for deduplication.

TradingView documents a three-second request timeout. Set the application acceptance target below one second and return only after durable commit; durable asynchronous processing avoids external calls in the request path. [TradingView webhook setup](https://www.tradingview.com/support/solutions/43000529348-how-to-configure-webhook-alerts/)

## Persistence

| Table | Essential fields and constraints |
| --- | --- |
| `instruments` | Stable ID, country, venue, canonical symbol; unique listing identity; explicit provider aliases |
| `social_observations` | Instrument, provider, observed/fetched times, rank, mentions, previous mentions, upvotes, quality, fixture flag |
| `fuel_observations` | Instrument, fetched time, nullable metrics, per-metric source as-of dates, units, quality, fixture flag |
| `market_events` | Instrument, source, producer ID, event ID, occurred/received times, sanitized payload, payload hash, status, attempts, error; unique source/producer/event ID |
| `evaluations` | Instrument, trigger ID, rule version, config fingerprint, tier, reason codes, evidence IDs, evaluated time, valid-until |
| `ticker_states` | Instrument primary key, current tier, latest evaluation, valid-until, latest market ordering key; historical peak is separate |
| `alert_intents` | ID, evaluation, trigger, tier, mode, priority, rendered message, expiry, status, attempt count, retry time, claim lease, Telegram message ID; unique instrument/tier/trigger/rule-version/mode |
| `alert_cooldowns` | Instrument/tier/mode primary key, next-allowed-at; reservation and intent creation in the same transaction |
| `job_runs` | Job name, start/end, success, counts, redacted error, next attempt |

Store times in UTC with one documented representation. Per-metric as-of dates must not become fresh merely because a poll was recent. Missing values remain null. Keep only redacted source payloads where needed; never persist webhook credentials.

SQLite uses WAL, foreign keys, a bounded busy timeout, and migrations. Put the database on persistent local disk, not a shared network volume. A durable inbox is the `market_events` table; a durable outbox is `alert_intents`, not an additional broker.

## Delivery semantics

Every event has at most one logical intent per unique key. Per-tier cooldowns are rolling windows, not clock-hour buckets. Tier 4 has its own cooldown and can escalate immediately after Tier 3. When one event qualifies for both, create only Tier 4. Cancel queued lower-tier intents superseded by a newer higher tier; an already transmitted message cannot be recalled by this mechanism.

Reserve the cooldown when creating the intent, including dry-run intents. Retries reuse that intent and do not re-reserve. A failed or expired intent retains its reservation until the normal window ends to prevent a failure storm. A fresh event is required for another alert after cooldown.

The outbox supports at-least-once send attempts until a retry or freshness limit. If Telegram accepted a message but the response was lost, retry can create a duplicate. Do not claim exactly-once Telegram delivery. Include a stable alert ID in the message. Use `pending`, `sending`, `retry_wait`, `sent`, `dry_run`, `expired`, `cancelled`, and `dead_letter` states.

Claim work with an atomic lease longer than the HTTP timeout; recover expired leases after restart. Recheck that the original evidence is unexpired, current evidence still supports at least the target tier, and rule/config version is unchanged before each attempt. Never send an old market alert hours after recovery. Dry-run records are terminal and are never replayed when live delivery is enabled.

## Runtime and failures

Run one Uvicorn worker and one service replica. Lifespan starts bounded, cancellable loops and closes HTTP/database resources on shutdown. Development reload is acceptable; the production service must not use reload or start one scheduler per worker. Maintain monotonic polling deadlines and UTC event times.

Provider failures produce degraded health and bounded retries with jitter. Do not replace real observations with fixtures on failure. Cached evidence remains usable only until its original expiry. A failed database write returns `503`, never a successful acknowledgment. TradingView documents retries for some server errors; duplicates must therefore be harmless. [TradingView resubmission](https://www.tradingview.com/support/solutions/43000735201-webhook-resubmission/)

Retry transient Telegram errors and honor `retry_after` where supplied. Authentication or destination failures become actionable dead letters. Log IDs, latency, job status, suppression reasons, and evidence ages without secrets. Expose liveness separately from readiness/degraded-provider status. Protect inspection routes with an admin credential; public health is minimal.

## Growth boundary

Move to PostgreSQL and a dedicated worker only when multiple replicas or demonstrated write contention require it. Keep adapter contracts and pure rules stable through that migration. Dashboard, direct market feeds, predictive exhaustion, and options data remain in ROADMAP.md.
