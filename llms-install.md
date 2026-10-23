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
2. No API key is needed to connect. These 5 tools are free and need no key: `catalog`, `whats_new`, `estimate_cost`, `get_sample`, `topic_search`.
3. To verify, call `catalog` with `{}`. It lists every tool with its price unit and markets.
4. Paid tools need a key. Call `request_key`, show the approval link to the user, and wait until they approve it and set a monthly budget. In this chat the key is then bound to the session; for a fixed key, add `"headers": { "Authorization": "Bearer <KEY>" }` to the entry above. Call `estimate_cost` first and always pass `max_lima` on paid calls; a call never charges above it.

Markets: `kr` (Naver + Google), `jp` (Google), `us` (Google, alpha). Pass `market` on every data call.

Docs: https://daas.listeningmind.ai/llms.txt · Repository: https://github.com/listeningmind/listeningmind-mcp · Support: m-support@listeningmind.com
