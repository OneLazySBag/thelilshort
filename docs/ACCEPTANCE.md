# Acceptance checks

These checks are required of the future implementation. None has been run against an application in this design pack.

## Pure rule tests

| Case | Required result |
| --- | --- |
| No observations | Tier 0; no alert |
| Qualifying social only | Tier 1; never creates an intent |
| Tier 1 + passing fresh Fintel branch | Tier 2; silent by default |
| Tier 2 alerts explicitly enabled | One eligible Tier 2 intent, subject to cooldown |
| Tier 3 threshold event + prerequisites | Tier 3, one intent |
| Tier 4 threshold event + stronger fuel | Tier 4, one urgent intent; no same-event Tier 3 intent |
| Strong market event without Tier 1 or Tier 2 | No Tier 3/4 |
| Rank 50 vs 51, growth 300 vs just below | Exact documented boundary behavior |
| Previous mentions zero/null | No divide-by-zero or artificial infinite growth |
| Null/stale Fintel field | Only valid passing branches can qualify |
| Short-interest data freshly fetched but too old | Cannot qualify from that metric |
| One fuel branch expires while another passes | Predicate remains active until last passing branch expires |
| Market event exceeds TTL or source becomes invalid | Current state downgrades; history remains |
| Tier 4 notifications disabled | No fallback Tier 3 notification |
| New event just before/at cooldown end | Suppress before; allow at boundary |
| Recent Tier 3 then Tier 4 | Tier 4 may escalate immediately |
| Changed rule version during cooldown | Cooldown still applies |
| Mixed fixture and live evidence | Never live-send |

Use injected time and table-driven fixtures. Include realistic scale/percentage and non-finite number cases. Keep rule tests independent of FastAPI and network access.

## Persistence and route tests

- Wrong/missing secret, excessive body, unknown keys, string booleans, NaN/infinity, unsupported producer, unmapped listing, and invalid dates fail without leaking request values.
- A valid request is committed before `202`; injected DB failure returns `503` with no apparent success.
- Simultaneous exact duplicate requests yield one inbox record, one logical evaluation path, and no duplicate intent.
- Same event key with different sanitized content returns `409`; secret rotation alone does not change the payload hash.
- An exact committed duplicate arriving after TTL returns duplicate without reprocessing; a new stale event is rejected.
- A late older event cannot regress state. A later source poll cannot retroactively qualify an already received market event.
- Two eligible concurrent events cannot both reserve the same instrument/tier/mode cooldown.
- Process restart recovers pending inbox and outbox records. Separate connections and a real temporary SQLite file exercise constraints; in-memory SQLite alone is insufficient for concurrency tests.
- Current-tier queries compute freshness even if the expiry loop has not run.

## Delivery and adapter tests

- Fixture mode makes zero external network requests and never accepts a live send configuration.
- Dry-run creates terminal `dry_run` intents and never changes them to live sends after a restart/config change.
- Telegram transient failures retry the same intent; permanent failures become visible dead letters; provider retry delays and message IDs are preserved.
- Expired evidence, changed config/rule version, or a now-lower current tier cancels pending sends. Superseded lower-tier work is not sent after a higher-tier candidate is queued.
- Simulate a lost Telegram response after acceptance and document possible duplicate delivery with stable alert ID. Do not assert exactly-once behavior.
- Fintel permission failure yields unavailable/degraded status with no fabricated metric; quotas and timeouts do not crash unrelated jobs.
- Sanitize numeric strings and malformed ApeWisdom rows; partial pages do not claim complete coverage.
- Unit conversions and every supported live Fintel field are backed by provider documentation and sanitized contract fixtures.
- Inspection routes require authorization and never return secrets. Logs/validation responses do not contain webhook tokens or Telegram token-bearing URLs.

## Demonstration and release gates

The offline demonstration walks a synthetic ticker through Tier 1, Tier 2, Tier 3, Tier 4, a duplicate, cooldown suppression, and expiry. Capture evidence IDs and dry-run intent counts. No credentials are required.

Before a live pilot, separately record evidence for Fintel entitlements/schema/units; TradingView indicator compilation and chart values; actual alert coverage; HTTPS authentication/redaction; Telegram destination verification; restart recovery; and persistent-disk backup/restore. A local fixture pass is not a live integration pass.

The public webhook path should acknowledge durable acceptance within one second in a representative local load check, with external adapters deliberately delayed. Record workload and environment with that result.

Completion report: changed files, checks actually run, results, skipped/manual checks, known limitations, and the next task. Do not mark a task complete based on a plan or assumed result.
