---
name: lm-demand-check
description: Quick demand check for a keyword with ListeningMind raw search data in Korea, Japan or the US (alpha), giving monthly volume, trend, competition and the related searches people make. Use for fast sizing of a product idea, a keyword list, or a campaign term before deeper analysis.
---

# Demand check

Uses two raw tools: `keyword_info` and `intent_finder`. They are priced in Lima for the data a call returns; `estimate_cost` (free) shows the cost before any run.

## Connection

This skill uses the ListeningMind MCP server at `https://daas.listeningmind.ai/mcp` (Streamable HTTP). If its tools are not available, tell the person how to connect it (add the URL as a custom connector or MCP server) and stop.

## Spending rules

- Prices are in Lima (1 Lima = KRW 100). Before each paid call, call `estimate_cost` with the tool name and the exact arguments, and show the person the typical and maximum Lima.
- Never make a paid call until the person has said yes in this conversation.
- Pass `max_lima` equal to the maximum from `estimate_cost`; the call never charges above it. If you get 401, call `request_key`, show the approval link and wait; the key is then attached to this conversation.

## Evidence rules

- Use only numbers that are in the result. Search volume is monthly search interest, not sales, market share or buyers.
- Journey steps are aggregated search transitions, not individual sessions.
- State the market, the as-of date and the quality result (`quality.passed`). If the result was delivered not charged, say it missed the quality rule and treat it as a hypothesis.
- Japan and the US have no gender or age data. Do not describe demographics for them.
- Write in the person's language.

## Steps

1. Agree on up to 10 keywords and the market (kr, jp or us; us is in alpha). Plan one `keyword_info` call for the list plus one `intent_finder` call for the main keyword, run `estimate_cost` for each, and tell the person the total.
2. After a yes, call `keyword_info` with `keywords`, `market` and `max_lima`.
3. For the main keyword, call `intent_finder` with `keyword`, `market`, `limit: 30` and `max_lima`.

## Output format

- **Size**: monthly average volume per keyword, with competition and CPC.
- **Direction**: volume_trend and, if monthly volume is present, the peak months.
- **What people add to the search**: group the related searches into 3 to 6 plain groups (price, comparison, problem, usage, brand) and name the largest.
- **Next step**: if the person needs reasons and barriers, suggest the topic brief (`lm-intent-brief`) and quote its cost with `get_quote`.
