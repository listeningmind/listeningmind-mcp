---
name: lm-non-customer
description: Explains why people who search for a category do not buy, or buy something else, using ListeningMind constraint, avoidance, threshold, expectation, pitfall and alternative data from real searches in Korea, Japan or the US (alpha). Use when someone asks about non-customers, drop-off reasons, barriers to purchase or why a category is not growing.
---

# Non-customer report

Survey panels mostly reach people who already buy. Search records also contain people who looked and did not buy. This skill reads the parts of a topic graph that describe hesitation and rejection.

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

1. Agree on the topic and market. Run the same quote and order flow as for a topic (product `intent_kg`).
2. From `get_order_result`, use these elements: constraint, avoidance, threshold, expectation, pitfall, trade-off, and from the entity layer: alternative.
3. Group the nodes into four kinds of barrier:
   - **Cannot**: constraints (budget, time, space, rules, compatibility).
   - **Will not**: avoidance (fear, complaints, risk).
   - **Not good enough**: thresholds and expectations the category does not meet.
   - **Something else instead**: alternatives and trade-offs.
4. Rank barriers by monthly volume. Quote the node text.

## Report format

- **Biggest barrier** in one sentence with its volume.
- **Barrier table**: kind, node, monthly volume, what it suggests.
- **What would remove it**: one action per top barrier, and the evidence a brand would need (for example a measured spec that answers a threshold).
- **Limits**: as-of date, quality result, and that search volume is interest, not lost sales.
