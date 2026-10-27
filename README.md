# ListeningMind Intent Data — remote MCP server

[![ListeningMind MCP connector](https://glama.ai/mcp/connectors/ai.listeningmind.daas/listening-mind/badges/score.svg)](https://glama.ai/mcp/connectors/ai.listeningmind.daas/listening-mind)

**Not search volume: the reasons people buy.** Make agents intent-intelligent.

ListeningMind gives AI agents intent intelligence from real searches: what people search, in what order, and why, before they choose a brand. It reads **search paths** (the searches people made before and after a keyword), **intent clusters** (how people frame a category) and **category entry points** (the buying situations where demand starts), plus the criteria people choose on, what they avoid and the thresholds that make them hesitate. Markets: **Korea and Japan, with the US in alpha** (Google, plus Naver in Korea). Use it over **MCP**, **REST (OpenAPI)** or **A2A**.

| | |
|---|---|
| MCP endpoint (Streamable HTTP) | `https://daas.listeningmind.ai/mcp` |
| Registry name | `ai.listeningmind.daas/intent-data` |
| Docs for agents | https://daas.listeningmind.ai/llms.txt |
| Server card | https://daas.listeningmind.ai/.well-known/mcp/server-card.json |
| OpenAPI | https://daas.listeningmind.ai/openapi.json |
| Website | https://daas.listeningmind.ai ([한국어](https://daas.listeningmind.ai/ko/) · [日本語](https://daas.listeningmind.ai/ja/)) |
| Pricing | https://daas.listeningmind.ai/en/pricing |

The server is hosted, so there is nothing to install or build. This repository holds the public metadata ([`server.json`](server.json)), agent install notes ([`llms-install.md`](llms-install.md)) and [agent skills](skills/).

## What agents can do with it
- Ask why people buy a product or category and get its buying situations, goals, decision criteria, thresholds, brands and pitfalls, with monthly searches and a source link to cite (`why_people_buy`, free).
- Follow search paths before and after a keyword to see how people move toward a choice.
- Split a category into the intent clusters people actually think in.
- Read stored topic maps with 15 intent elements (category entry points, choice criteria, constraints, avoidance, thresholds and more).
- Look up keyword demand, related searches and what AI Overviews say and cite.

Coverage: US about 1.5 billion keywords (alpha), Japan about 700 million, Korea about 400 million.

## Connect
No key and no OAuth are needed to connect. Any client that supports remote Streamable HTTP MCP servers can use the URL above.

**Claude Code**
```bash
claude mcp add --transport http listeningmind https://daas.listeningmind.ai/mcp
```

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
1. "Using ListeningMind, why do shoppers in the US hesitate before buying a mattress?"
2. "Using ListeningMind, show me a sample of related searches for running shoes in the US."
3. "Find stored ListeningMind topics about gaming chairs in the US."
4. "What tools does ListeningMind offer, and what do they cost?"

## Tools (18)
- **Free, no key:** `why_people_buy`, `ask`, `catalog`, `topic_search`, `get_sample`, `estimate_cost`
- **Key and free credits:** `claim_free_credits`, `verify_code`, `request_key` (free), `my_account` (free, key needed)
- **Search data (paid, 5 Lima a call):** `keyword_info`, `intent_finder`, `path_finder`, `cluster_finder`, `ai_overview`
- **Stored topics, 15 intent elements (paid):** `intent_elements`
- **Orders:** `place_order` (paid, the quoted price), `get_order_result` (free, key needed)

`whats_new` (free) and older tool names (`get_quote`, `find_samples`, `wait_for_order`, `order_status`, `cancel_order`, `my_balance` and others) still answer for existing clients.

Markets: `kr` (Naver + Google), `jp` (Google), `us` (Google, alpha). Pass `market` on every data call. Call `catalog` for each tool's price, markets and input schema.

## Keys and pricing
- Free tools need no key. For paid tools the agent either calls `claim_free_credits` with the person's e-mail and then `verify_code` with the 6-digit code they receive (the key is attached to the chat), or calls `request_key` and shows the approval link; the person approves it once and sets a monthly Lima budget. Over REST or with a fixed key, send `Authorization: Bearer <key>` or `LM-API-KEY: <key>`.
- Prices are in **Lima**, the ListeningMind usage unit: 10 Lima = USD 1. A data call (MCP or API) is 5 Lima for up to 1,000 rows. Call `estimate_cost` first and pass `max_lima`; a call never charges above it.
- Free plan: every account (the e-mail that approves a key) gets 40 Lima a month (8 data calls), refilled on the 1st of each month (Korea time), not carried over. `why_people_buy` answers already-mapped categories free, plus two new categories a day.
- Plans and Lima packs: https://daas.listeningmind.ai/en/pricing
- Rate limit: 120 requests per 60 seconds.

## Agent skills
Four skills in [`skills/`](skills/) run on this server: `lm-demand-check`, `lm-intent-brief`, `lm-non-customer`, `lm-three-markets`. Each folder has a `SKILL.md`. Install them with:
```bash
npx skills add listeningmind/listeningmind-mcp
```
They are also available as zip files at https://daas.listeningmind.ai/en/skills.

---

## 한국어
**검색량이 아니라, 사는 이유를 드립니다.** 리스닝마인드는 AI 에이전트에 실제 검색에서 읽어 낸 인텐트 인텔리전스를 제공합니다. 사람들이 브랜드를 고르기 전에 무엇을, 어떤 순서로, 왜 검색하는지를 검색 경로(키워드 앞뒤에 실제로 한 검색), 인텐트 클러스터(사람들이 카테고리를 나누는 방식), 카테고리 진입점(CEP, 수요가 시작되는 구매 상황)으로 보여 줍니다. 한국과 일본을 지원하고, 미국은 알파 단계입니다(구글, 한국은 네이버 포함).

- MCP 서버 주소: `https://daas.listeningmind.ai/mcp`. 헤더나 인증 없이 바로 연결됩니다.
- 키 없이 쓰는 무료 도구: `why_people_buy`, `ask`, `catalog`, `topic_search`, `get_sample`, `estimate_cost`.
- 유료 데이터 도구는 리마(Lima)로 과금됩니다. 10리마 = 1달러이고, 데이터 호출(MCP·API) 1회는 5리마(최대 1,000행)입니다. `max_lima`를 넘겨 과금되지 않고, 실행 전에 `estimate_cost`로 비용을 확인할 수 있습니다.
- 무료 요금제: 모든 계정(키를 승인한 이메일)이 매달 40리마(데이터 호출 8회)를 받습니다. 매달 1일(한국 시간)에 다시 채워지고, 남은 리마는 다음 달로 넘어가지 않습니다.
- 키는 대화 안에서 `claim_free_credits`(이메일) → `verify_code`(6자리 코드)로 받거나, `request_key`로 승인 링크를 받아 사람이 월 리마 예산을 정하면 쓸 수 있습니다.
- 데이터 규모: 미국 약 15억 키워드(알파), 일본 약 7억, 한국 약 4억.
- 클라이언트별 연결 방법은 위 **Connect**를 참고하세요.

## 日本語
**検索ボリュームではなく、買う理由を。** ListeningMindは、実際の検索から読み取ったインテントインテリジェンスをAIエージェントに提供します。人がブランドを選ぶ前に、何を、どの順番で、なぜ検索するのかを、検索経路（キーワードの前後に実際に行われた検索）、インテントクラスター（人がカテゴリをどう捉えているか）、カテゴリーエントリーポイント（CEP、需要が始まる購買状況）で示します。日本と韓国に対応し、米国はα版です（Google、韓国はNaverも対象）。

- MCPサーバー: `https://daas.listeningmind.ai/mcp` 。ヘッダーや認証なしで接続できます。
- キー不要の無料ツール: `why_people_buy`、`ask`、`catalog`、`topic_search`、`get_sample`、`estimate_cost`。
- 有料データツールはLima（リマ）で課金されます。10リマ＝1米ドルで、データ呼び出し（MCP・API）は1回5リマ（1,000行まで）です。`max_lima`を超えて課金されることはなく、`estimate_cost`で実行前に費用を確認できます。
- 無料プラン: すべてのアカウント（キーを承認したメール）に毎月40リマ（データ呼び出し8回分）を付与します。毎月1日（韓国時間）に補充され、残りは翌月に繰り越しません。
- キーはチャット内で`claim_free_credits`（メール）→`verify_code`（6桁のコード）で取得するか、`request_key`の承認リンクで担当者が月間Lima予算を設定すると使えます。
- データ規模: 米国約15億キーワード（α版）、日本約7億、韓国約4億。
- クライアント別の接続方法は上の **Connect** をご覧ください。

---

## Publisher and support
Built by **Ascent AI Co., Ltd.** ListeningMind holds 21 patents.
Support: m-support@listeningmind.com · Terms: https://www.listeningmind.com/en/terms/service · Privacy: https://www.listeningmind.com/en/terms/privacy

Repository: https://github.com/listeningmind/listeningmind-mcp · License: [MIT](LICENSE)
