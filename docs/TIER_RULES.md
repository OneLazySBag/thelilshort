# Tier rules and notification policy

Rule version: `v1-draft-1`. These are explicit starting heuristics from the earlier discussion, with engineering defaults added for reproducibility. They are not empirically validated, calibrated probabilities, or proof of an active squeeze. Record changes in DECISIONS.md and increment the rule version.

## Evidence model

Evaluate the highest qualifying tier at a supplied UTC `now`; return `tier`, `reason_codes`, `evidence_ids`, and `valid_until`. Tier 0 means no active candidate. A missing prerequisite blocks all higher tiers. No tier is sticky forever.

Use actual source timestamps when available and retain `fetched_at` separately. For ApeWisdom, if no source publication time exists, use fetch time as the observation time and mark that provenance as inferred. For Fintel, missing or ambiguous metric dates make that metric ineligible until its semantics are established.

| Evidence | Draft maximum age | Rule |
| --- | --- | --- |
| Qualifying social observation | 24 hours | Most recent qualifying observation in the window; a nonqualifying later rank does not immediately end the watch |
| Fintel fetch | 6 hours | A recent fetch is required in addition to source-age limits |
| Squeeze score as-of | 24 hours | Only if the account supplies a documented score and timestamp |
| Borrow fee as-of | 6 hours | Annualized percentage, not a decimal fraction |
| Short-interest settlement date | 30 calendar days | Delayed structural fuel; never call it live short interest |
| TradingView market event | 300 seconds | Measured from event time, not receipt or retry time |
| Future clock tolerance | 30 seconds | Beyond this, reject rather than silently adjusting time |

Age bounds are inclusive. Future times within tolerance have effective age zero. A dated short-interest value uses calendar-date age; its validity ends at the next UTC date after the allowed age. Carry original time precision. Neither an unavailable Fintel response nor a missing field equals zero.

## Tier 1 — social attention

Require at least 10 mentions and either rank at or above the top 50 (`rank <= 50`) or mention growth of at least 300%.

`mention_growth_pct = 100 * (mentions - mentions_24h_ago) / mentions_24h_ago` only when the denominator is positive. Otherwise growth is null; rank can still qualify. Rank must be positive and counts nonnegative. The minimum mention count is a proposed noise filter, not a previously confirmed user requirement.

Use ApeWisdom only. No invented `raw_score`, direct Reddit scraping, or extra social provider.

## Tier 2 — squeeze fuel

Require active Tier 1 and at least one fresh, documented Fintel metric satisfying:

- Squeeze score >= 80 on a documented 0–100 scale.
- Short interest percentage of float >= 20.
- Annualized borrow fee percentage >= 50.

This retains the earlier OR policy. Record exactly which branch passed. A single high metric is a screening heuristic, not independent confirmation of every squeeze factor. Store available shares and days to cover as context; neither qualifies by itself in V1. A later experiment may evaluate multiple-metric confirmation without changing this rule silently.

For each metric, select its most recent valid known source observation, not the historical maximum. A newer explicit unavailable value invalidates that metric; a failed fetch preserves the prior value only until its original expiry. Use per-metric timestamps when a response mixes update cadences.

## Tier 3 — market confirmation

Require active Tier 2 and one accepted fresh TradingView event satisfying all of:

- `intraday_return_pct >= 20`.
- `relative_volume >= 5`.
- `above_vwap == true` or `vwap_reclaim == true`.
- `condition_name == "RVOL_VWAP_BREAKOUT"`.
- Supported producer/script version, one-minute completed bar, and regular-session scope.

## Tier 4 — urgent active squeeze check

Require Tier 3 predicates on the same event, plus all of:

- `intraday_return_pct >= 30`.
- `relative_volume >= 8`.
- `above_vwap == true`.
- At least one fresh stronger Fintel metric: squeeze score >= 85, short interest percentage of float >= 25, or annualized borrow fee percentage >= 75.

Tier 4 does not require that a Tier 3 notification was previously delivered. Persist the highest tier and all passing prerequisite reasons; do not emit Tier 3 and Tier 4 for the same event.

## Metric definitions

All percentages use percentage units: `34.5` means 34.5%, not 0.345. Price must be finite and positive. Relative volume must be finite and nonnegative; booleans must be JSON booleans, not strings.

The proposed TradingView producer `squeeze-v1` defines intraday return against the previous regular-session close and VWAP as the current regular-session volume-weighted HLC3. RVOL is completed one-minute bar volume divided by the mean volume of the previous 20 completed one-minute regular-session bars in the same session. It is not a same-time-of-day 20-session RVOL; changing that definition requires a producer and rule review. No market event is produced until sufficient bars and a positive denominator exist.

`above_vwap` compares the completed close with that bar's VWAP. `vwap_reclaim` means the preceding completed close was at/below its VWAP and the current close is above current VWAP. A reclaim with `above_vwap=false` contradicts this producer definition and is rejected. The logical OR remains in Tier 3 for an explicit event contract, even though a valid reclaim currently implies above VWAP.

V1 covers regular-session US equities. Corporate actions, delayed feeds, and extended-session calculations require explicit handling before broadening that scope. Unsupported or ambiguous symbols are quarantined instead of guessed.

## State and time ordering

Only a newly accepted market event can initiate Tier 3/4 promotion. Social/fuel polls can promote Tier 1/2 and can downgrade/expire higher tiers, but cannot resurrect an old market event into a new alert.

For a market evaluation, select prerequisite observations that were already received by the service when that event was received; also disallow source timestamps later than event time plus clock tolerance. Evaluate their freshness at processing time. This prevents a later poll from retroactively supplying a missing prerequisite. If no qualifying prerequisites exist, store the event as evaluated with an explicit suppression reason; do not defer it indefinitely waiting for fuel.

Order events by occurred time and deterministic event ID within each approved instrument/producer stream. An event older than the applied watermark is recorded as out-of-order and cannot regress state or alert. Exact duplicates do no work. A newer nonqualifying market event immediately drops the market tier while preserving any still-valid Tier 1/2.

Use the latest market evidence plus live freshness checks on reads; preserve historical evaluations separately. For OR branches, the fuel predicate lasts while at least one passing branch remains valid. Overall expiry is the earliest prerequisite expiry, using the longest-lived passing branch within an OR predicate. Reevaluate when any relevant evidence changes.

## Notification policy

| Tier | Setting | Default |
| --- | --- | --- |
| 0–1 | Hard prohibition | No alert intent |
| 2 | `SEND_TIER_2_ALERTS` | `false` |
| 3 | `SEND_TIER_3_ALERTS` | `true` |
| 4 | `SEND_TIER_4_ALERTS` | `true` |

`TELEGRAM_DRY_RUN=true` makes eligible alerts local terminal records, not real sends. Real delivery requires live inputs, configured credentials, enabled tier policy, and dry-run disabled. Fixture inputs can never produce a live send, including mixed live/fixture evidence.

Select the highest qualifying tier first. If that tier's notification is disabled, do not fall back to a lower-tier message. Apply a 60-minute rolling cooldown per instrument/tier/delivery mode. Rule-version changes do not bypass cooldown. Tier 2 also uses cooldown when enabled.

Persist suppressed evaluations with reasons such as `tier_silent`, `missing_fuel`, `stale_market`, `cooldown_active`, `out_of_order`, `fixture_evidence`, and `superseded`. A new event after cooldown may re-alert even without a tier transition. Polling or heartbeat ticks alone never create repeat Tier 3/4 alerts.

Urgent means a prominent Tier 4 heading and priority in the delivery queue. It does not promise bypassing a phone's notification settings.
