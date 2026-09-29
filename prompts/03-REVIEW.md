# Prompt — independent correctness review

Review the implemented SqueezeAlert V1 against AGENTS.md, ARCHITECTURE.md, docs/TIER_RULES.md, docs/CONTRACTS.md, and docs/ACCEPTANCE.md. Use the squeeze-review skill.

Resolve architecture, skill-source, code, and test paths from the application repository, not the separate private OpenClaw workspace. Preserve that workspace and identity; keep any private notes outside Git. Inspect actual code and run relevant offline tests. Trace a market event through authentication, durable acceptance, prerequisite selection, tier evaluation, cooldown reservation, delivery retry, and restart. Check source dates, missing metrics, OR-branch expiry, out-of-order handling, concurrency, and fixture/live isolation.

Look specifically for accidental Tier 1/2 messages, duplicate Tier 3/4 sends from the same event, stale fuel qualifying as fresh, Telegram calls before commit, multiple polling loops, secret leakage, and claims of exactly-once external delivery.

Return prioritized actionable findings with file location, triggering case, impact, and minimal remediation. Distinguish verified failures, plausible untested risks, and stylistic suggestions. This prompt authorizes review and offline checks; provide fixes as a concrete proposal unless implementation fixes are also part of the active request.

Report local-demo readiness separately from live-pilot readiness. Name unverified Fintel access, Pine compilation, TradingView coverage, and Telegram destination where applicable. Do not send real messages, change production configuration, or certify financial performance.
