# @fload-ai/mcp

MCP server for Fload — gives AI assistants access to your mobile app analytics, reviews, growth metrics, and more.

[![npm version](https://img.shields.io/npm/v/@fload-ai/mcp)](https://www.npmjs.com/package/@fload-ai/mcp)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Quick Start

### 1. Get your API key

Go to [platform.fload.com](https://platform.fload.com/settings/api-keys) > Settings > API Keys > Create Key.

### 2. Add to Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS):

```json
{
  "mcpServers": {
    "fload": {
      "command": "npx",
      "args": ["-y", "@fload-ai/mcp"],
      "env": {
        "FLOAD_API_KEY": "fload_sk_your_key_here"
      }
    }
  }
}
```

Restart Claude Desktop. You'll see Fload tools available.

### 3. Add the Fload skill (optional, recommended)

The Fload skill teaches your AI agent how to use Fload tools effectively — common workflows, tips, and best practices.

```bash
npx skills add fload-ai/mcp --skill fload
```

Works with Claude Code, Cursor, Cline, GitHub Copilot, and [18+ other agents](https://skills.sh).

## Tools

| Tool | Description |
|------|-------------|
| `list_apps` | List all apps in your organization |
| `get_app_details` | Get detailed app info by ID or bundle ID |
| `get_reviews` | Get reviews with filtering (rating, date, platform) |
| `discover_metrics` | Discover available metrics for an app |
| `get_metrics` | Query metric timeseries (30+ metrics, multi-dimension) |
| `discover_dimensions` | Discover breakdown dimensions (country, platform, etc.) |
| `list_agents` | List AI agents (review, monitoring, growth, etc.) |
| `get_agent_details` | Get agent config for a specific app |
| `get_agent_run_history` | Agent execution history |
| `get_anomalies` | Detected metric anomalies (surges/declines) |
| `get_ads_performance` | Ad campaign data (ASA, Google, Meta, TikTok) |
| `get_growth_audit` | Comprehensive growth assessment |
| `get_growth_score` | 0-100 growth score with grade |
| `get_forecasts` | Valuation forecasts and trend analysis |
| `get_dashboard_overview` | Organization portfolio overview |
| `list_pending_actions` | Pending AI-generated review replies |
| `approve_action` | Approve a pending review reply |
| `reject_action` | Reject a pending review reply |

## Configuration

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `FLOAD_API_KEY` | Yes | — | Your API key (`fload_sk_...`) |
| `FLOAD_API_URL` | No | `https://api.fload.com` | API URL (override for self-hosted) |

Or use `~/.fload/config.json`:

```json
{
  "apiKey": "fload_sk_your_key_here",
  "apiUrl": "https://api.fload.com"
}
```

## What is Fload?

[Fload](https://fload.com) is an AI-powered platform for mobile app publishers. It connects to App Store Connect, Google Play Console, ad platforms (Apple Search Ads, Google Ads, Meta, TikTok), Stripe, and RevenueCat to give you:

- 📊 Unified analytics across all your apps
- 🤖 AI-powered review management (auto-reply with approval flow)
- 🚨 Anomaly detection (revenue drops, download spikes, crash surges)
- 📈 Growth scoring and audits
- 💰 App valuations and forecasts
- 📢 Ad performance across all platforms

## License

MIT
