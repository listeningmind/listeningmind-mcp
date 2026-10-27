# Installing the ListeningMind MCP server

This is a hosted remote MCP server (Streamable HTTP). There is nothing to download or build.

1. Add this entry to the MCP settings file (Cline: `cline_mcp_settings.json`):
```json
{
  "mcpServers": {
    "listeningmind": {
      "type": "streamableHttp",
      "url": "https://daas.listeningmind.ai/mcp",
      "disabled": false,
      "autoApprove": []
    }
  }
}
```
   Keep `"type": "streamableHttp"`. Without it the client falls back to SSE, and this server answers GET with 405.
2. No API key is needed to connect. These tools are free and need no key: `why_people_buy`, `ask`, `catalog`, `topic_search`, `get_sample`, `estimate_cost`.
3. To verify, call `why_people_buy` with `{"q": "mattress"}` (free). It returns the buying situations, decision criteria, thresholds, brands and pitfalls for the category, with a source link. `catalog` with `{}` lists every tool with its price and markets.
4. Paid tools need a key. In a chat, call `claim_free_credits` with the person's e-mail, then `verify_code` with the 6-digit code they receive; the key is attached to the chat and the free monthly Lima are added. Or call `request_key`, show the approval link to the person, and wait until they approve it and set a monthly budget. For a fixed key, add `"headers": { "Authorization": "Bearer <KEY>" }` to the entry above. Call `estimate_cost` first and always pass `max_lima` on paid calls; a call never charges above it.

Prices: 10 Lima = USD 1. Data tools are priced per 1,000 results a call returns: keyword_info 10 Lima (ad metrics) or 100 (all metrics), intent_finder 20, path_finder 20 (keywords only) or 400 (with paths), cluster_finder 20 (keywords only) or 500 (with clusters), ai_overview 300. The price is set before the call; estimate_cost shows it. Every account gets 100 Lima at sign-up, then 20 Lima a month, refilled on the 1st (Korea time). Prices are proposals until launch: https://daas.listeningmind.ai/en/pricing

Markets: `kr` (Naver + Google), `jp` (Google), `us` (Google, alpha). Pass `market` on every data call.

Docs: https://daas.listeningmind.ai/llms.txt · Repository: https://github.com/listeningmind/listeningmind-mcp · Support: m-support@listeningmind.com
