---
name: fload
description: Use when the user has Fload MCP tools available and asks about mobile app analytics, reviews, growth metrics, ad performance, App Store Optimization or Fload's agents. Teaches effective use of Fload's MCP tools for mobile app publishers.
license: MIT
metadata:
  author: fload-ai
  version: '2.3.0'
---

# Fload: Mobile App Analytics via MCP

You have Fload's MCP tools. Use them to help the user understand how their apps are doing, manage reviews, track growth and work with Fload's agents.

Connect: `claude mcp add --transport http --scope user fload https://api.fload.com/mcp` in Claude Code. Every other client, and the API key setup: the package README (https://www.npmjs.com/package/@fload-ai/mcp).

## What Is Fload?

Fload is a platform for mobile app publishers. It brings together App Store Connect, Google Play Console, ad platforms (Apple Search Ads, Google Ads, Meta, TikTok), RevenueCat, Stripe and analytics tools, and runs agents that look after an app's reviews, App Store listing (ASO) and Apple Ads.

The server gives each tool's name, description and inputs, and tells you at `initialize` how its tools work together. This skill covers the common paths.

## Ground rules

- Start with `listAssets`. Every app-level tool takes the app's id as `assetId`; pass it on every call. A tool that needs an app and gets none refuses rather than guessing.
- Changes never publish in one step. A tool that changes a listing, Apple Ads, a review reply or an experiment stages an item in the Fload inbox. Read it with `getActionItem`, and approve it with `approvePendingAction` or `commandActionItems` only when the person asked for that decision.
- A big keyword bid change asks first: `asa_update_bid` answers `NEEDS_CONFIRMATION` with the current bid, the new bid, the change in percent and a `confirmId`, and changes nothing. Show the person both bids and the change, and call `asa_update_bid_confirm` only after they say yes. `BID_CHANGED` means Apple's bid moved since they were asked: ask again with the bid as it is now. With an API key the confirm answers `CONFIRM_NEEDS_PERSON`: tell the person to make the change from their own login.
- `null` means unknown, not zero. A metric listed as unavailable was not measured: never report it as 0.
- A result marked as an error says why in plain words. Fix the call or tell the person; never retry the same call unchanged.

## Common workflows

### "How is my app doing?"

1. `listAssets` to find the app.
2. `getMetrics` for revenue and downloads (`discoverMetrics` first when you don't know the metric names). Quote its totals; they are aggregated the right way for each metric.

### "Show me my reviews"

1. `getReviews` with filters (rating 1-2 for negative ones, `replied: false` for unanswered ones).
2. `generateReviewReply` drafts one reply, `generateReviewReplies` many; each is staged in the inbox.
3. `review_stage_reply` stages the person's own text as the reply.
4. To send, approve the items (`commandActionItems` with command "approve", one target per item) when the person asks.

### "What's happening with my ads?"

1. `ads_campaigns` for an app's campaigns on every ad platform, with spend and installs.
2. The `asa_*` tools for Apple Search Ads in depth (keywords, search terms, wasted spend, bid suggestions).

### "Which keywords should we test in Apple Ads, and which are missing from our metadata?"

1. `keyword_reference` with the app and a storefront (`country`): one row per keyword, joining the app's own Apple Ads keywords (campaign, ad group, match type, status, bid, last 30 days of spend, taps and installs) with its rank, volume, difficulty, relevance and where the listing carries it. Read only, from Fload's stored data.
2. `view: "candidates"` lists the keywords the app ranks for that no Apple Ads keyword targets (the most searched first); `view: "metadata_gaps"` lists the Apple Ads keywords the listing does not carry; `view: "all"` is the full reference list.
3. A keyword whose campaign targets several countries has `storefront.kind: "campaign_covers"`: say "the campaign covers N countries" and never present its numbers as this country's. Tell the person every `notSeen` item (negatives are not stored yet; no ranks where the storefront has no crawl): an unread value is unknown, never "none".

### "Give me a full business overview"

1. `getDashboardOverview` for the portfolio.
2. `getMetrics` per app for its trend.

### "How can I improve my store listing?"

1. `getAsoSummary` for the app's ASO health, `aso_recommendations` for proposed changes.
2. `aso_tracked_keywords` for the keywords Fload tracks, their ranks and how much each is searched on the App Store (its `search_popularity` reading, out of 100); `market_*` tools for the market.
3. `aso_update_listing_text` stages a listing change in the inbox.

### "Which keywords does my app rank for, and did Fload's change move them?"

1. `aso_ranked_keyword_change` for the app's latest live change (or an `experimentId`): the top 10/30/50 counts before and after, per storefront, and the keywords newly in the top 50, newly 51-100, or lost. Quote the `headline` total (the changed storefronts); a `not_measured` storefront is "not measured yet", never zero.
2. `aso_keyword_daily_ranks` for the app's own rank per keyword per day (at most 200 keywords and 120 days), to line a keyword list up against another tool's. A null rank on a `completeDays` day means not in the top 100; a `noDataDays` day has no data, never "unranked".

### "What are my competitors doing?"

1. `market_competitors` for the app's rivals. Pass `country` for each rival's rating there and its rank against yours on your top 3 keywords over 21 days (App Store).
2. `competitor_moves` for what the rivals changed this week, market by market: listing, rank, rating, Apple Ads and in-app events. A type marked `not_read` was not read in the window: say so, never "no moves". On Google Play (`store: android`) there is no Apple Ads or in-app event type.
3. `competitor_keywords` for the head-to-head grid on your top keywords in one market. A `beyond` cell is read and outside the top 100; a `not_read` cell has no read, never a rank. It takes `store` too.
4. `competitor_apple_ads` for your share of the ads shown on your name and keywords, who leads, and the rivals bidding there (App Store only). A `not_read` keyword has no share captured, never 0%.
5. `competitor_rival_changes` for the changes this organization made to the list. From a person's own login, `competitor_add_rival` puts an app first on it (its App Store id, or its package name on Google Play), `competitor_remove_rival` keeps one off it, and `competitor_undo_rival_change` takes a change back. Each takes `store` (ios or android) for an app on both stores. Each change is this organization's only; nothing is sent to Apple or Google.

### "Which searches bring my Android app installs?"

1. `play_search_terms` for the app's Play Console search terms over the 28 days that end on its Play report's latest day: store listing acquisitions per term, most first. Google Play only. The totals cover all countries together, so never split or sum them per market; a term here is first-party evidence, not a validated keyword. `no_rows` means no Play report on file yet, never "no searches".

### "Set up install attribution (the Fload SDK)"

1. `attribution_sdk_status` for where each app stands: `no_key`, `waiting_for_first_install` or `receiving`, its write keys (each active one whole, to give the developer again) and the installs each sent.
2. `attribution_write_key_create` for a new key (an owner or admin, from their own login; never offered to an API key): the answer has the whole `fload_pk_…` key, and `attribution_sdk_status` shows it again later, so a lost key never needs a new one. The app passes it once at launch: `Fload.configure(key: "<key>")` on iOS, `Fload.configure(this, key = "<key>")` on Android; guide: https://fload.com/docs/attribution. Apple Ads keyword revenue needs it.
3. `attribution_write_key_revoke` only for a key that leaked or no live build uses: every build with it stops sending installs (rotate first).
4. For a developer setting the SDK up: `attribution_sdk_status` with the app's `assetId` also returns `setupPrompt`, one prompt with the app's key that a coding agent (Claude Code, Cursor and the like) follows in the app's repository on any stack. Hand it over whole and unchanged.

### "Did Fload's changes affect my downloads (in a country)?"

1. `getDownloadImpact` for the app (add `country` for one storefront).
2. Judge listing changes on `organicSearch` against the same weeks last year, never on total, paid or browse downloads.

### "What are the agents doing?"

1. `getAgentStatus` for an app's agents, `agent_run_history` and `agent_activity` for what one did.
2. `getPendingActions` and `getTaskLedger` for the work waiting in the inbox.

## Older tool names

On an API key connection, the names of `@fload-ai/mcp` 0.12 and before (`list_apps`, `get_metrics`, `approve_action`, …) still work as aliases of the tools above; each says which tool it stands for. Prefer the names above.
