---
name: squeeze-integrations
description: Build or diagnose ApeWisdom, Fintel, TradingView, and Telegram contracts for SqueezeAlert without fabricating access or metrics.
user-invocable: true
---

# SqueezeAlert integrations

Paths are installation placeholders, not skill-directory-relative paths. Resolve `{{REPO_ROOT}}` and `{{WORKSPACE_ROOT}}` to their distinct absolute locations using {{REPO_ROOT}}/OPENCLAW_SETUP.md before installation. Run application commands in {{REPO_ROOT}}; keep private memory and diary in {{WORKSPACE_ROOT}}. Never copy runtime notes into repository templates.

Use for source adapters, webhook ingress, the TradingView producer, and Telegram transport. Read {{REPO_ROOT}}/docs/CONTRACTS.md, {{REPO_ROOT}}/docs/TIER_RULES.md, and {{REPO_ROOT}}/docs/SOURCES.md.

1. Identify the provider, exact required field, its meaning, units, timestamp, entitlement, and error behavior.
2. Use current official documentation and sanitized responses when available. Record unavailable information explicitly. Do not guess hidden endpoints or scrape around missing access.
3. Keep the normalized application contract stable; coordinate shared-type changes with the lead.
4. Test success, missing values, malformed data, timeouts, quota/permission failures, and unit conversions with mocked HTTP.
5. Check provenance and freshness; a new fetch cannot refresh an old source date.
6. For TradingView, verify both the receiver contract and the producer/coverage setup. Replay IDs remain stable. A webhook receiver alone is not a scanner.
7. For Telegram, keep dry-run separate from send transport, redact token-bearing URLs, and retain message IDs/error outcomes.

Do not change tier thresholds to make an adapter demo pass. Do not silently fall back from live data to fixtures. Do not send real messages merely to validate a transport unless the active user request covers that send.

Output: field mapping, implemented adapter behavior, contract-test evidence, and remaining live/manual dependencies.
