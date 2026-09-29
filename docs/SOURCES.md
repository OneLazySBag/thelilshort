# Sources and verification boundaries

Checked 2026-09-29. Use these official sources when implementing; current documentation may differ from the installed OpenClaw release or an account's capabilities.

| Source | What was verified |
| --- | --- |
| [OpenClaw workspace](https://docs.openclaw.ai/concepts/agent-workspace) | Workspace location, operating/persona files, memory, local skill folder |
| [OpenClaw skills](https://docs.openclaw.ai/tools/skills) | SKILL.md frontmatter and workspace discovery |
| [OpenClaw memory](https://docs.openclaw.ai/concepts/memory) | Durable memory, dated notes, and diary distinction |
| [OpenClaw dreaming](https://docs.openclaw.ai/concepts/dreaming) | Native memory consolidation and DREAMS.md output |
| [ApeWisdom API](https://apewisdom.io/api/) | Filter, pagination, and example fields |
| [Fintel API](https://api.fintel.io/) | Published route catalog and endpoint availability caveat |
| [Fintel developer hub](https://fintel.io/api-developers) | Programmatic data access offering |
| [TradingView webhook setup](https://www.tradingview.com/support/solutions/43000529348-how-to-configure-webhook-alerts/) | JSON POST, timeout, network setup, credential caution |
| [TradingView resubmission](https://www.tradingview.com/support/solutions/43000735201-webhook-resubmission/) | Retry behavior requiring idempotent ingress |
| [TradingView authentication](https://www.tradingview.com/support/solutions/43000680459-webhook-authentication/) | Documented TLS certificate authentication option |
| [TradingView technical alerts](https://www.tradingview.com/support/solutions/43000763315-getting-started-with-technical-alerts/) | Producer configuration is separate from a receiver |
| [Telegram Bot API](https://core.telegram.org/bots/api#sendmessage) | sendMessage interface |

The referenced Squeeze Exhaustion Model conversation supplied project context and provisional thresholds. This pack preserves the four source roles and tier notification policy, while making implementation assumptions explicit.

Unverified: installed OpenClaw version/configuration, Fintel account access and quotas, real provider responses, Pine code compilation, actual TradingView feed/alert coverage, Telegram credentials, public hosting, and statistical performance. No source validates the numeric screening thresholds used here.
