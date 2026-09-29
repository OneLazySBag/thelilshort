# Decision register

Date: 2026-09-29. `required` means supplied product scope; `proposed` means an engineering recommendation to implement under the build prompt. Neither label means empirically validated.

| ID | Status | Decision | Reason / consequence |
| --- | --- | --- | --- |
| D001 | Required | Alert-only, no execution | Product purpose is manual candidate review |
| D002 | Required | ApeWisdom → Fintel → TradingView → Telegram | Preserve the agreed source roles |
| D003 | Required | Tier 1 silent; Tier 2 opt-in; Tier 3/4 enabled | Preserve notification policy |
| D004 | Required | FastAPI and SQLite | Requested local MVP stack |
| D005 | Proposed | One modular monolith and one worker | Simple deployment; avoid duplicate loops |
| D006 | Proposed | SQLAlchemy/aiosqlite + Alembic | Explicit transactions and schema evolution |
| D007 | Proposed | Supervised asyncio loops | Avoid a separate scheduler for small fixed intervals |
| D008 | Proposed | Pure tier engine and separate policy | Deterministic tests and independent delivery behavior |
| D009 | Proposed | SQLite inbox/outbox with cooldown reservations | Durable acknowledgment, recovery, and duplicate suppression |
| D010 | Proposed | Fixtures and dry-run first; provenance guard | Reproducible build without credentials |
| D011 | Proposed | Freshness by individual metric | Recent fetch does not imply recent source data |
| D012 | Proposed | Draft thresholds in TIER_RULES.md | Make earlier heuristics executable without claiming validation |
| D013 | Proposed | Minimum 10 mentions; no undefined social score | Avoid zero-base growth and unspecified scoring |
| D014 | Proposed | Available shares context-only in V1 | No agreed low/falling-share definition or reliable history yet |
| D015 | Proposed | One-minute regular-session producer with explicitly defined RVOL | Prevent mismatched metric meanings |
| D016 | Proposed | Most recent qualifying social observation holds watch for 24h | Reduce rank churn while retaining bounded expiry |
| D017 | Proposed | Latest evidence per fuel metric; no historical maxima | Avoid cherry-picking obsolete high values |
| D018 | Proposed | One highest-tier intent per market event | Avoid back-to-back Tier 3/4 messages for one trigger |
| D019 | Proposed | Dedicated OpenClaw workspace; project skills; manual reflection | Clear file discovery and separation from app runtime |
| D020 | Proposed | Native DREAMS.md diary; DREAMING.md procedure; ROADMAP.md ideas | Separate memory consolidation from future product work |

Adoption compatibility decisions (2026-09-29; user-authorized workspace separation and sanitized publication):

| ID | Status | Decision | Reason / consequence |
| --- | --- | --- | --- |
| D021 | Required; clarifies D019 | Existing agent workspace stays separate from the application repository; preserve agent identity | Install adapted instructions and skills with absolute paths; do not repoint the workspace or rely on symlinks |
| D022 | Required; clarifies D020 | Private memory/diary stay workspace-only; publish sanitized seed templates | DREAMING.md is manual guidance; adoption does not change native dreaming settings; HEARTBEAT.md stays comment-only/inactive |
| D023 | Required | Publish no environment files, including examples | Keep proposed non-secret settings in docs/CONFIGURATION.md instead |

Open dependencies: Fintel account access, TradingView producer verification and coverage, Telegram destination, hosting, OpenClaw installed version. No predictive exhaustion model or automatic threshold tuning is authorized by this design.

For future changes record: date, decision ID, status, context, choice, alternatives considered, affected tests/config, and source of authorization or assumption. Supersede an earlier decision explicitly; do not rewrite history as if a new preference always existed.
