# Proposed configuration template

This is a design specification, not a runnable configuration or a record of installed secrets. No `.env` file (including examples) belongs in publication. After implementation, create a private local `.env` in the application repository from the block below; never copy this Markdown file directly as an environment file. Keep blank secret fields local and out of Git. Application commands run from the repository root, so the relative SQLite path resolves there.

```dotenv
APP_ENV=development
LOG_LEVEL=INFO
DATABASE_URL=sqlite+aiosqlite:///./data/squeeze-alert.db
DATA_MODE=fixture
FINTEL_MODE=fixture
TELEGRAM_DRY_RUN=true
TELEGRAM_BOT_TOKEN=
TELEGRAM_CHAT_ID=
TRADINGVIEW_WEBHOOK_SECRET=
ADMIN_API_TOKEN=
FINTEL_API_KEY=
SEND_TIER_2_ALERTS=false
SEND_TIER_3_ALERTS=true
SEND_TIER_4_ALERTS=true
RULE_VERSION=v1-draft-1
SOCIAL_POLL_SECONDS=300
FUEL_POLL_SECONDS=900
SOCIAL_MAX_PAGES=3
SOCIAL_MAX_AGE_SECONDS=86400
FUEL_FETCH_MAX_AGE_SECONDS=21600
FUEL_SCORE_MAX_AGE_SECONDS=86400
BORROW_MAX_AGE_SECONDS=21600
SHORT_INTEREST_MAX_AGE_DAYS=30
MARKET_MAX_AGE_SECONDS=300
MAX_FUTURE_SKEW_SECONDS=30
SOCIAL_MIN_MENTIONS=10
SOCIAL_MAX_RANK=50
SOCIAL_MIN_GROWTH_PCT=300
TIER_2_MIN_SQUEEZE_SCORE=80
TIER_2_MIN_SHORT_INTEREST_PCT=20
TIER_2_MIN_BORROW_FEE_PCT=50
TIER_3_MIN_RETURN_PCT=20
TIER_3_MIN_RVOL=5
TIER_4_MIN_RETURN_PCT=30
TIER_4_MIN_RVOL=8
TIER_4_MIN_SQUEEZE_SCORE=85
TIER_4_MIN_SHORT_INTEREST_PCT=25
TIER_4_MIN_BORROW_FEE_PCT=75
ALERT_COOLDOWN_MINUTES=60
HTTP_TIMEOUT_SECONDS=5
DELIVERY_MAX_ATTEMPTS=5
DELIVERY_LEASE_SECONDS=30
WEBHOOK_MAX_BYTES=16384
TRADINGVIEW_ALLOWED_PRODUCER=squeeze-v1-main
TRADINGVIEW_ALLOWED_SCRIPT_VERSION=squeeze-v1
```

These defaults preserve the supplied draft rules. The implementation must validate placeholders, threshold ordering, source modes, and fixture/dry-run isolation as specified in [TOOLS.md](../TOOLS.md) and [TIER_RULES.md](TIER_RULES.md). No credentials or live destination have been supplied.
