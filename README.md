# ListeningMind Intent Data — remote MCP server

<!-- Glama score badge: add after the Glama connector is listed, e.g.
[![ListeningMind MCP connector](https://glama.ai/mcp/connectors/GLAMA_NAMESPACE/GLAMA_NAME/badges/score.svg)](https://glama.ai/mcp/connectors/GLAMA_NAMESPACE/GLAMA_NAME)
-->

**Make agents Intent-Intelligent.**

ListeningMind gives AI agents search-intent data: what people search, in what order, and why, before they choose a brand. It covers **Korea and Japan, with the US in alpha** (Google, plus Naver in Korea). Use it over **MCP**, **REST (OpenAPI)** or **A2A**.

| | |
|---|---|
| MCP endpoint (Streamable HTTP) | `https://daas.listeningmind.ai/mcp` |
| Registry name | `ai.listeningmind.daas/intent-data` |
| Docs for agents | https://daas.listeningmind.ai/llms.txt |
| Server card | https://daas.listeningmind.ai/.well-known/mcp/server-card.json |
| OpenAPI | https://daas.listeningmind.ai/openapi.json |
| Website | https://daas.listeningmind.ai ([한국어](https://daas.listeningmind.ai/ko/) · [日本語](https://daas.listeningmind.ai/ja/)) |

The server is hosted, so there is nothing to install or build. This repository holds the public metadata ([`server.json`](server.json)), agent install notes ([`llms-install.md`](llms-install.md)) and [agent skills](skills/).

## What agents can do with it
- Look up keyword demand and related searches for a market.
- Follow search paths before and after a keyword to see how people move toward a choice.
- Read stored topic maps of search intent and find the situations that lead people to a category.
- Check how a keyword appears in AI search results.

## Connect
No key and no OAuth are needed to connect. Any client that supports remote Streamable HTTP MCP servers can use the URL above.

**Cursor** (`~/.cursor/mcp.json` or `.cursor/mcp.json`)
```json
{ "mcpServers": { "listeningmind": { "url": "https://daas.listeningmind.ai/mcp" } } }
```

**VS Code (GitHub Copilot)** (`.mcp.json` at the workspace root or `~/.copilot/mcp-config.json`)
```json
{ "mcpServers": { "listeningmind": { "type": "http", "url": "https://daas.listeningmind.ai/mcp" } } }
```
Older `.vscode/mcp.json` form: `{ "servers": { "listeningmind": { "type": "http", "url": "https://daas.listeningmind.ai/mcp" } } }`. You can also run **MCP: Add Server → HTTP** from the command palette.

**Windsurf** (`mcp_config.json`)
```json
{ "mcpServers": { "listeningmind": { "serverUrl": "https://daas.listeningmind.ai/mcp" } } }
```

**Cline** (`cline_mcp_settings.json`). Keep `"type": "streamableHttp"`; without it Cline uses SSE and the server answers GET with 405.
```json
{ "mcpServers": { "listeningmind": { "type": "streamableHttp", "url": "https://daas.listeningmind.ai/mcp", "disabled": false, "autoApprove": [] } } }
```

**Gemini CLI**
```bash
gemini mcp add --scope user --transport http listeningmind https://daas.listeningmind.ai/mcp
```

**Claude Desktop (local config, via `mcp-remote`)** (`claude_desktop_config.json`)
```json
{
  "mcpServers": {
    "listeningmind": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://daas.listeningmind.ai/mcp", "--transport", "http-only"]
    }
  }
}
```
The same `mcp-remote` bridge works for any client that only supports stdio servers.

**Try it** with these free prompts:
1. "What tools does ListeningMind offer, and what do they cost?"
2. "Using ListeningMind, show me a sample of related searches for running shoes in the US."
3. "Find stored ListeningMind topics about gaming chairs in the US."

## Tools (29)
- **Free, no key (5):** `catalog`, `whats_new`, `estimate_cost`, `get_sample`, `topic_search`
- **Raw search data:** `keyword_info`, `intent_finder`, `path_finder`, `cluster_finder`, `ai_overview`
- **Stored topics (15 intent elements):** `intent_elements`, `intent_graph`, `topic_build`
- **Products, quotes and orders:** `list_products`, `find_samples`, `get_quote`, `place_order`, `wait_for_order`, `order_status`, `get_order_result`, `list_my_orders`, `cancel_order`, `my_notices`
- **Ask a market question:** `ask`, `run_outcome`, `outcome_status`
- **Account:** `request_key`, `my_balance`, `buy_lima`

Markets: `kr` (Naver + Google), `jp` (Google), `us` (Google, alpha). Pass `market` on every data call. Call `catalog` for each tool's price, markets and input schema.

## Keys and pricing
- Paid tools need a key. The agent calls `request_key` and shows the approval link; a person approves it once and sets a monthly Lima budget. In a chat the key is bound to the MCP session. Over REST or with a fixed key, send `Authorization: Bearer <key>` or `LM-API-KEY: <key>`.
- Prices are in **Lima**, the ListeningMind usage credit (1 Lima = KRW 100). Call `estimate_cost` first and pass `max_lima`; a call never charges above it.
- Current prices and any free allowance: https://daas.listeningmind.ai/en/pricing
- Rate limit: 120 requests per 60 seconds.
<!-- Free allowance (add only after deploy is confirmed): every account 10,000 credits (130 Lima) a month; company-domain accounts also 100,000 credits (1,300 Lima) once at signup (one per company domain). -->

## Agent skills
Four skills in [`skills/`](skills/) run on this server: `lm-demand-check`, `lm-intent-brief`, `lm-non-customer`, `lm-three-markets`. Each folder has a `SKILL.md`. Install them with:
```bash
npx skills add listeningmind/listeningmind-mcp
```
They are also available as zip files at https://daas.listeningmind.ai/en/skills.

---

## 한국어
리스닝마인드는 AI 에이전트에 검색 의도 데이터를 제공합니다. 사람들이 브랜드를 고르기 전에 무엇을, 어떤 순서로, 왜 검색하는지 보여 줍니다. 한국과 일본을 지원하고, 미국은 알파 단계입니다(구글, 한국은 네이버 포함).

- MCP 서버 주소: `https://daas.listeningmind.ai/mcp`. 헤더나 인증 없이 바로 연결됩니다.
- 키 없이 쓰는 무료 도구: `catalog`, `whats_new`, `estimate_cost`, `get_sample`, `topic_search`.
- 유료 데이터 도구는 리마(Lima, 1리마 = 100원)로 과금되며, `max_lima`를 넘겨 과금되지 않습니다. 실행 전에 `estimate_cost`로 비용을 확인할 수 있습니다.
- 유료 도구는 `request_key`로 키를 요청하고, 사람이 승인 링크에서 월 리마 예산을 정하면 쓸 수 있습니다.
- 클라이언트별 연결 방법은 위 **Connect**를 참고하세요.

## 日本語
ListeningMindはAIエージェントに検索意図データを提供します。人がブランドを選ぶ前に、何を、どの順番で、なぜ検索するのかが分かります。日本と韓国に対応し、米国はα版です（Google、韓国はNaverも対象）。

- MCPサーバー: `https://daas.listeningmind.ai/mcp` 。ヘッダーや認証なしで接続できます。
- キー不要の無料ツール: `catalog`、`whats_new`、`estimate_cost`、`get_sample`、`topic_search`。
- 有料データツールはLima（1 Lima = 100ウォン）で課金され、`max_lima`を超えて課金されることはありません。`estimate_cost`で実行前に費用を確認できます。
- 有料ツールは`request_key`でキーを申請し、承認リンクで担当者が月間Lima予算を設定すると使えます。
- クライアント別の接続方法は上の **Connect** をご覧ください。

---

## Publisher and support
Built by **Ascent AI Co., Ltd.** ListeningMind holds 21 patents.
Support: m-support@listeningmind.com · Terms: https://www.listeningmind.com/en/terms/service · Privacy: https://www.listeningmind.com/en/terms/privacy

Repository: https://github.com/listeningmind/listeningmind-mcp · License: [MIT](LICENSE)
