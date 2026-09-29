# Integration and API contracts

This is the application contract to implement. Provider examples are starting references, not proof that an account has access. All timestamps are UTC ISO 8601 on the wire.

## Adapter interfaces

| Interface | Input | Output |
| --- | --- | --- |
| `SocialProvider.fetch_trending` | Filter and page | Validated social observations with source metadata |
| `FuelProvider.fetch_fuel` | Canonical instrument and provider alias | Nullable metrics with individual as-of dates and availability reasons |
| `Notifier.send` | Alert ID, rendered text, configured destination | Provider message ID, retryable failure, or permanent failure |

Use injected httpx clients, timeouts, and a clock. A fixture implementation obeys the same contracts and always marks its observations synthetic. Adapter outputs do not contain raw credentials. No live-to-fixture fallback.

## ApeWisdom

Use the documented `all-stocks` filter at `https://apewisdom.io/api/v1.0/filter/all-stocks`; follow documented pagination. Map `ticker`, `rank`, `mentions`, `upvotes`, `rank_24h_ago`, and `mentions_24h_ago`. Safely parse numeric strings. Set a page cap, request timeout, and backoff. Deduplicate by instrument within each poll. [ApeWisdom API](https://apewisdom.io/api/)

Do not assume the API supplies a normalized social score, quote price, verified exchange mapping, or source publication timestamp. A malformed row is quarantined with a reason; a malformed whole response degrades the job. Do not turn partial success into an assertion of complete market coverage.

## Fintel

Create one Fintel adapter with explicit `fixture`, `import`, and `live` modes. Import mode accepts a locally supplied, schema-validated authorized export; record file provenance and the original data dates. It does not scrape the website.

The current official API reference lists short-interest, borrow-rate, and short-squeeze leaderboard routes under `/v1`; its documentation also describes individually gated availability. The account's actual permission and response schema must be verified before enabling live mode. [Fintel API reference](https://api.fintel.io/)

Map into these application fields: `squeeze_score`, `short_interest_pct_float`, `borrow_fee_pct_annualized`, `shares_available`, and `days_to_cover`. Each field has a value, source as-of timestamp/date, source field name, and availability status. Do not equate short volume with short interest or assume a borrow-rate response includes a squeeze score. Do not derive percentage of float without a documented compatible denominator.

Normalize units only after reading provider definitions and a sanitized real response. Missing entitlement is `unavailable`, not zero. Cache by instrument and metric cadence. Do not fetch every trending symbol every few seconds. Delay live work until endpoint access, quotas, timestamps, and entitlement are known; the fixture build can continue.

## TradingView ingress

Route: `POST /webhook/tradingview`. Accept JSON only, enforce a small body limit (16 KiB proposed), reject unknown fields, and authenticate before storing any event. Do not log request bodies or echo validation input values containing credentials.

The compatibility MVP uses a dedicated high-entropy, rotatable `secret` scoped only to this receiver, verified with a constant-time comparison and removed before persistence/hash calculation. It is not a TradingView account password or a provider credential. It authenticates possession of the token, not the sender's identity. HTTPS and redaction are required for live ingress. TradingView cautions against sensitive credentials in webhook bodies; production hardening should evaluate its documented TLS client-certificate mechanism at the reverse proxy. Do not invent custom authorization headers or HMAC signatures that the configured TradingView producer cannot send. [Webhook setup](https://www.tradingview.com/support/solutions/43000529348-how-to-configure-webhook-alerts/), [webhook authentication](https://www.tradingview.com/support/solutions/43000680459-webhook-authentication/)

Illustrative synthetic event, not a current market quote:

```json
{
  "schema_version": 1,
  "secret": "REPLACE_WITH_DEDICATED_RECEIVER_TOKEN",
  "producer_id": "squeeze-v1-main",
  "script_version": "squeeze-v1",
  "event_id": "squeeze-v1-main:NASDAQ:TEST:2026-09-29T15:31:00Z:1",
  "symbol": "TEST",
  "exchange": "NASDAQ",
  "occurred_at": "2026-09-29T15:31:00Z",
  "timeframe": "1",
  "session": "regular",
  "bar_closed": true,
  "price": 6.42,
  "intraday_return_pct": 34.5,
  "relative_volume": 8.7,
  "above_vwap": true,
  "vwap_reclaim": false,
  "condition_name": "RVOL_VWAP_BREAKOUT"
}
```

Use a frozen matching clock in tests. A live smoke helper must generate a current event time and a new event ID; simply posting this dated example later must be rejected as stale.

`producer_id` identifies an allowlisted alert stream; V1 uses one active stream per instrument and timeframe. `event_id` is stable across retransmission and unique per producer/instrument/completed bar. The Pine script must construct these application fields; they are not all built-in TradingView placeholders. The service hashes canonical JSON after stripping the secret. Same key and same hash is a duplicate; same key and different hash is a conflict.

| Response | Meaning |
| --- | --- |
| `202` with event ID and `accepted` | Valid, authenticated event committed to inbox |
| `200` with event ID and `duplicate` | Exact committed duplicate; no new work |
| `401` | Missing or invalid receiver credential |
| `409` | Event key reused with different content |
| `413` / `415` | Body too large / unsupported media type |
| `422` | Invalid schema, timestamp, instrument mapping, producer, or metrics |
| `503` | Durable acceptance failed; caller may retry |

After authentication/schema validation, resolve exact existing duplicates before applying the incoming-event freshness rejection, so a delayed retry of a committed event remains idempotent. Conflicts are never processed. New stale/future events return `422`. A durably accepted event that becomes stale in the queue is recorded as expired without alerting.

Normalize whitespace and case but preserve share-class punctuation. Never blindly strip everything before a colon or guess a different listing. Resolve `(country, exchange, symbol)` through explicit aliases; unresolved symbols are quarantined. Fixtures use a clearly synthetic instrument and never create a real alias automatically.

## TradingView producer and coverage

Implement an indicator, not a strategy that generates orders. Compute the metrics defined in TIER_RULES.md, emit once per completed one-minute regular-session bar when market thresholds qualify, and include the agreed script version. If nonqualifying updates are not sent, market state expires by TTL; document that immediate downgrade requires a newer market observation.

Webhook reception does not create TradingView alerts. A newly discovered ApeWisdom ticker receives no Tier 3/4 evidence unless covered by an active TradingView alert. Maintain a manually managed coverage list in V1 and document missing coverage in inspection. Validate account capabilities and producer behavior in TradingView before calling the integration live-ready. Keep Pine compilation/manual chart verification as a separately reported check.

## Telegram

Use the Bot API `sendMessage` with environment-supplied bot token and chat ID. Start with plain text to avoid formatting injection. Parse provider success and message ID; honor documented error parameters such as `retry_after`. Do not print the token-bearing URL. [Telegram Bot API](https://core.telegram.org/bots/api#sendmessage)

Illustrative alert rendering:

```text
TIER 4 — URGENT MANUAL REVIEW
NASDAQ:TEST | $6.42 | +34.5% | RVOL 8.7x
Market: above VWAP; completed 1m regular-session bar
Fuel: borrow fee 82% annualized (as of <timestamp>)
Social: rank 12; 240 mentions (observed <timestamp>)
Market event: <timestamp> | Evidence age: <age>
Rule: v1-draft-1 | Alert ID: <stable-id>
Candidate for manual review; no trade instruction.
```

Show whichever fuel branch actually passed, its timestamp, and any delayed-data qualification. Do not label missing values as zero or present synthetic values without a conspicuous DRY RUN/FIXTURE marker. Tier 3 uses a standard heading. Tier 1 never reaches rendering/delivery.

## Inspection and health

`GET /health/live` reports process liveness. `GET /health/ready` reports whether the DB and essential event/delivery loops can accept work; separate degraded source flags from process health. Public responses contain no symbols, source payloads, tokens, or destination IDs.

Authenticated local-first inspection routes: `GET /api/tickers`, `GET /api/tickers/{instrument_id}`, and `GET /api/alerts`, with bounded pagination and redacted output. Show current versus historical tier, evidence ages, coverage status, reason codes, and delivery outcomes. No public debug dump.
