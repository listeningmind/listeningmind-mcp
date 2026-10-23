---
name: lm-intent-brief
description: Builds a decision brief for one topic from ListeningMind search data in Korea, Japan or the US (alpha), covering the 15 intent elements (buying situations, choice criteria, constraints, avoidance, thresholds and more). Use when someone asks why people buy or search in a category, what they choose by, or wants a category brief from real search behavior.
---

# Topic intent brief

Turns one topic (a category such as "dehumidifier", "日焼け止め", "running shoes") into a brief a marketer or planner can act on.

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

1. Agree on the topic and market (kr, jp or us). Use the market's language for the topic.
2. `find_samples` with product `intent_kg` and the topic words. If a stored topic matches, use it; otherwise the order will build it.
3. `get_quote` with product `intent_kg`, subject, market. Show the terms. If `options` offers both the stored result and a rebuild, show both with as-of date and age, and let the person choose.
4. After a yes, `place_order` with `quote_id` and `max_lima`, then `wait_for_order` until delivered. Then `get_order_result`.
5. Write the brief from `data.elements` and `data.entities`.

## Brief format

- **One-line read of the market**: what the top buying situations and the strongest avoidance say together.
- **Decision layer**: for each of CEP, occasion, nano intent, KBF, constraint, threshold, avoidance, RTB, list up to 3 nodes with their monthly volume. Skip empty elements and say they were empty.
- **Knowledge layer**: heuristic, convention, expectation, pitfall, trade-off, benchmark, stance, up to 3 each, quoting the node text.
- **Who and where**: brands, alternatives, channels and content needs from the entity layer.
- **What to do**: 3 to 5 actions, each tied to a named node (for example "answer the threshold '18L' on the product page").
- **Limits**: as-of date, quality result, and the first two items of `limits`.
