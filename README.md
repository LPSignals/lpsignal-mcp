# LPSignal MCP server

Hosted [Model Context Protocol](https://modelcontextprotocol.io) server for **[LPSignal](https://lpsignal.app)**: lets AI
agents (Claude, Cursor, VS Code, any MCP client) find concentrated-liquidity LP opportunities ranked by **net APR =
fees − impermanent loss** of specific price ranges, from hourly backtests on exact on-chain fees.

Uniswap v3/v4, PancakeSwap v3, Aerodrome and Velodrome Slipstream on Ethereum, BNB Chain, Base, Arbitrum, Optimism,
Polygon and Robinhood Chain.

```
https://api.lpsignal.app/v1/mcp
```

Remote server, Streamable HTTP. Nothing to install or run. Listed in the official MCP Registry as
`app.lpsignal/lpsignal`.

## Connect

No key needed: you get the free view. With an LPSignal API key (from your [account page](https://lpsignal.app/account))
add the header `Authorization: Bearer lps_…` to get your plan's view.

**Claude Code**

```bash
claude mcp add --transport http lpsignal https://api.lpsignal.app/v1/mcp
# with a key:
claude mcp add --transport http lpsignal https://api.lpsignal.app/v1/mcp --header "Authorization: Bearer lps_…"
```

**Cursor** (`~/.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "lpsignal": {
      "url": "https://api.lpsignal.app/v1/mcp",
      "headers": { "Authorization": "Bearer lps_…" }
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`)

```json
{
  "servers": {
    "lpsignal": {
      "type": "http",
      "url": "https://api.lpsignal.app/v1/mcp",
      "headers": { "Authorization": "Bearer lps_…" }
    }
  }
}
```

**Claude (desktop / web)**: Settings → Connectors → Add custom connector, URL `https://api.lpsignal.app/v1/mcp` (the
free view; this dialog does not take headers).

Leave out `headers` for the free view.

## Tools

All read-only.

| Tool | What it answers |
|---|---|
| `list_pools` | Pools ranked by their best range's net APR over 1 h / 24 h / 7 d / 30 d, by chain and pool class |
| `find_pair_pools` | Every pool of a token pair across chains, DEXes and fee tiers (`USDC` + `WETH`, any case) |
| `get_pool` | One pool: every range it is evaluated on, with fee APR, impermanent loss, net APR, time in range, exact ticks |
| `backtest_range` | Backtest any ±x% range (or full range) on a pool over the last 1–30 days |
| `list_signals` | Latest signals: opportunities (`net_apr`, `burst`, `hot_pool`) and risk alerts (`depeg`, `tvl_outflow`) |
| `signal_track_record` | How past signals really did, re-backtested on their exact ticks |
| `smart_lp_leaderboard` | Wallets ranked by what their closed LP positions made versus holding |
| `my_account` | With a key: your plan, subscriptions and custom alert rules |

APRs come back as percent text (`"42.0%"`), and every pool comes with a link to its page on lpsignal.app.

**Free view** (no key): risk alerts live, opportunity signals 24 hours after they fire, smart-LP top 10 with masked
addresses. **Basic / Pro** keys: signals live, your custom-rule matches, the full leaderboard (Pro).
[Plans](https://lpsignal.app/pricing).

## Try asking

- "Which USDC/WETH pool and range paid the most after impermanent loss this week?"
- "Backtest a ±5% range on the top Base stable pool over 14 days."
- "Any stablecoin depegs or liquidity outflows in the last day?"
- "How have LPSignal's past opportunity signals actually performed?"

## Also

- REST API: [OpenAPI 3.1](https://api.lpsignal.app/v1/openapi.json) · [docs](https://lpsignal.app/docs)
- SDKs: [Node.js](https://github.com/LPSignals/lpsignal-node) (`npm i lpsignal`) · [Python](https://github.com/LPSignals/lpsignal-python) (`pip install lpsignal`)
- Live push: WebSocket, signed webhooks, Telegram

Figures are backtests of past on-chain data, not forecasts or financial advice. A range that paid well can lose money
if the price moves out of it.

## 中文

LPSignal 的托管 MCP 服务，地址 `https://api.lpsignal.app/v1/mcp`（Streamable HTTP），无需安装。AI agent 可以直接查询集中
流动性池的**净 APR（手续费 − 无常损失）**排名、交易对池子、任意区间回测、最新信号、信号历史表现和聪明 LP 排行。全部工具只读。
不带密钥为免费视图；在请求头加 `Authorization: Bearer lps_…`（[账户页](https://lpsignal.app/zh/account)生成）按你的套餐返回。
数据是历史回测，不是预测，也不构成投资建议。

## License

MIT
