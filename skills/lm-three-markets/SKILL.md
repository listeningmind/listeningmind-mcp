---
name: lm-three-markets
description: Compares one category across Korea, Japan and the US (alpha) with ListeningMind topic graphs, showing how buying situations, choice criteria, constraints and avoidance differ by market. Use when someone plans a launch or message in another country, or asks how Korean, Japanese and American consumers differ on a category.
---

# Three-market comparison

## Connection

This skill uses the ListeningMind MCP server at `https://daas.listeningmind.ai/mcp` (Streamable HTTP). If its tools are not available, tell the person how to connect it (add the URL as a custom connector or MCP server) and stop.

## Spending rules

- Prices are in Lima. Always call `get_quote` first and show the person its `say_to_user` line, the price and whether the result is stored or built new.
- Never call `place_order` with a price above 0 until the person has said yes in this conversation. A free test read (price 0) can go ahead.
- Pass `max_lima` equal to the quoted price. If you get 401, call `request_key`, show the approval link and wait; the key is then attached to this conversation.
- A build takes 2 to 6 minutes. Call `wait_for_order` until the status is `delivered` or `delivered_not_charged`. Give the person the status page link from `say_to_user` so they can pick it up in another chat.

## Evidence rules

- Use only numbers that are in the result. Search volume is monthly search interest, not sales, market share or buyers.
- Journey steps are aggregated search transitions, not individual sessions.
- State the market, the as-of date and the quality result (`quality.passed`). If the result was delivered not charged, say it missed the quality rule and treat it as a hypothesis.
- Japan and the US have no gender or age data. Do not describe demographics for them.
- Write in the person's language.

## Steps

1. Agree on the category and write it in each market's language (for example 제습기 / 除湿機 / dehumidifier). Show the three names and let the person correct them.
2. `get_quote` three times (product `intent_kg`, markets kr, jp, us). Add up the prices and show the total. Ask once for a yes on the total.
3. `place_order` for each, then `wait_for_order` for each until delivered. Builds can run at the same time.
4. Compare elements across the three results.

## Comparison format

- **Same everywhere**: nodes whose meaning appears in all three markets.
- **Only here**: for each market, the 3 strongest nodes that do not appear in the others, with volume.
- **Element table**: rows CEP, KBF, constraint, threshold, avoidance; columns kr, jp, us; top node and volume in each cell.
- **What to change per market**: message, proof point and channel, each tied to a node.
- **Limits**: as-of date per market, quality result per market. Volumes are not comparable as market size across countries because search engines and populations differ; compare rank within a market, not raw numbers across markets.
