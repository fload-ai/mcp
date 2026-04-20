# @fload-ai/mcp

MCP server for [Fload](https://fload.com) — connects any MCP-compatible AI client (Claude, ChatGPT, Cursor, VS Code, Cline) to your mobile app's App Store Connect / Google Play reviews, metrics, ad campaigns, anomalies, and ASO data.

37 tools across 10 domains. OAuth 2.1 for remote connections, API key for scripts and CI.

## Two ways to connect

### Remote (recommended) — OAuth 2.1 via `mcp-remote`

For Claude Desktop, Cursor, ChatGPT, and any client that supports remote MCP servers. No API keys to manage, per-organization scoping, revoke anytime from Fload Settings → Connected Apps.

**Claude Desktop** (`~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "fload": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.fload.com/mcp"]
    }
  }
}
```

Restart Claude Desktop. On first use, `mcp-remote` opens a browser for OAuth consent. Pick which Fload organization to share, approve scopes, done.

**Cursor:** Settings → MCP → Add remote server → `https://api.fload.com/mcp`. Cursor handles Dynamic Client Registration and the consent flow automatically.

**ChatGPT (Team / Enterprise):** Admin portal → Connectors → Add custom connector → server URL `https://api.fload.com/mcp`.

### Local stdio — API key

For scripts, CI, headless environments, or anyone who wants a long-lived machine credential. Install this package and point an MCP client at the binary:

1. Create an API key at [platform.fload.com/settings/api-keys](https://platform.fload.com/settings/api-keys).

2. Add to your MCP config:

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

   Or use `~/.fload/config.json`:

   ```json
   {
     "apiKey": "fload_sk_your_key_here",
     "apiUrl": "https://api.fload.com"
   }
   ```

## What you can ask your agent

- "Why did iOS installs drop yesterday?"
- "Draft replies to my last 5 one-star reviews in my brand voice, then approve the good ones."
- "What's my ROAS across all ad platforms this week?"
- "Audit my ASO for my top-grossing app and suggest title changes."
- "List anomalies across my portfolio this week — acknowledge the ones on my dummy app."

## Tools

37 tools grouped by domain. Full list with annotations + scope requirements at [fload.com/docs/mcp](https://fload.com/docs/mcp).

| Domain | Tools |
|---|---|
| Apps | `list_apps`, `get_app_details` |
| Reviews | `get_reviews`, `generate_review_reply`, `send_review_reply`, `translate_review` |
| Analytics | `discover_metrics`, `get_metrics`, `discover_dimensions` (30+ metrics, dimensional breakdowns) |
| Agents | `list_agents`, `get_agent_details`, `get_agent_run_history`, `trigger_agent_run`, `pause_agent`, `resume_agent`, `get_agent_activity` |
| Anomalies | `get_anomalies`, `get_anomaly_detail`, `acknowledge_anomaly`, `dismiss_anomaly` |
| Ads | `get_ads_performance` (Apple Search Ads, Google Ads, Meta Ads, TikTok Ads) |
| ASO | `get_aso_summary`, `get_aso_recommendations`, `get_aso_keywords`, `get_aso_experiments`, `get_aso_locale_snapshots`, `trigger_aso_analysis` |
| Growth | `get_growth_audit`, `get_growth_score` |
| Forecasting | `get_forecasts` |
| Dashboard | `get_dashboard_overview` |
| Actions | `list_pending_actions`, `approve_action`, `reject_action` |
| Chat | `list_conversations`, `get_conversation_messages`, `send_chat_message` |

Every tool carries an MCP `readOnlyHint` or `destructiveHint` annotation so clients can surface the scope of each action to users before calling.

## OAuth scopes (remote only)

16 scopes. Agents request the minimum they need; users approve per-scope on the consent screen and can revoke from [platform.fload.com/settings/connected-apps](https://platform.fload.com/settings/connected-apps).

- **OIDC identity:** `openid`, `email`, `profile`, `offline_access`
- **Reads:** `read:apps`, `read:reviews`, `read:analytics`, `read:anomalies`, `read:ads`, `read:aso`, `read:agents`
- **Writes:** `write:reviews`, `write:ads`, `write:aso`, `write:agents`, `write:chat`

## Configuration (stdio mode)

| Variable | Required | Default | Description |
|---|---|---|---|
| `FLOAD_API_KEY` | Yes (stdio) | — | API key, format `fload_sk_...` |
| `FLOAD_API_URL` | No | `https://api.fload.com` | API URL (override for self-hosted) |

## Issues & feedback

File bugs or feature requests at [github.com/fload-ai/mcp/issues](https://github.com/fload-ai/mcp/issues). For integration support: support@fload.com.

## License

MIT
