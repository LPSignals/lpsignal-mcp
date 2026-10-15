# LPSignal MCP Server — Uniswap & DeFi liquidity pool APR for AI agents

[![MCP Registry](https://img.shields.io/badge/MCP%20Registry-app.lpsignal%2Flpsignal-blue)](https://registry.modelcontextprotocol.io/v0.1/servers?search=app.lpsignal/lpsignal)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)

A hosted **Uniswap and DeFi MCP server** (Model Context Protocol) that lets AI agents — Claude, Cursor, VS Code Copilot,
any MCP client — find the best **concentrated-liquidity pools and price ranges**, ranked by **net APR: fees minus
impermanent loss**, from hourly backtests on exact on-chain fees. Ask your agent which Uniswap v3 range to LP in,
backtest a range, or check impermanent loss, and it answers with live data from [LPSignal](https://lpsignal.app).

```
https://api.lpsignal.app/v1/mcp
```

Remote server (Streamable HTTP): nothing to install, no API key needed to start. Official MCP Registry name:
`app.lpsignal/lpsignal`.

## Why net APR, not fee APR

DEX sites and most APR trackers show a pool's **fee APR**. What a liquidity provider keeps is the fee income of *their
price range* minus *impermanent loss*. LPSignal backtests 3–4 ranges per pool every hour over 1 hour, 24 hours, 7 days and
30 days, using each pool's exact `feeGrowthGlobal` counters rather than volume estimates, and ranks ranges by what they
netted. Every signal is re-backtested on its exact ticks afterwards and published, losses included.

## Supported chains and DEXes

| Chain | DEXes |
|---|---|
| Ethereum | Uniswap v3, Uniswap v4, PancakeSwap v3 |
| Base | Uniswap v3, Uniswap v4, Aerodrome Slipstream, PancakeSwap v3 |
| Arbitrum | Uniswap v3, Uniswap v4, PancakeSwap v3 |
| BNB Chain | PancakeSwap v3, Uniswap v3, Uniswap v4 |
| Optimism | Uniswap v3, Uniswap v4, Velodrome Slipstream |
| Polygon | Uniswap v3, Uniswap v4 |
| Robinhood Chain | Uniswap v3, Uniswap v4 |

Pools whose two tokens are established assets (stablecoins, ETH and BTC wrappers, liquid staking tokens, tokens over
$100M market cap). Uniswap v4 pools without hooks and with a static fee.

## Quick start

No key: the free view. With an LPSignal API key (from your [account page](https://lpsignal.app/account)) add the header
`Authorization: Bearer lps_…` for your plan's view.

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

All read-only: the server never touches funds or wallets.

| Tool | What it answers |
|---|---|
| `list_pools` | Liquidity pools ranked by their best range's net APR over 1 h / 24 h / 7 d / 30 d, by chain and pool class (stable, correlated, volatile) |
| `find_pair_pools` | Every pool of a token pair across chains, DEXes and fee tiers, e.g. `USDC` + `WETH` |
| `get_pool` | One pool: each range's fee APR, impermanent loss, net APR, time in range and exact ticks |
| `backtest_range` | Backtest any ±x% price range (or full range) on a pool over the last 1–30 days |
| `list_signals` | Latest LP signals: opportunities (`net_apr`, `burst`, `hot_pool`) and risk alerts (stablecoin `depeg`, `tvl_outflow`) |
| `signal_track_record` | How past signals really did, re-backtested on their exact ticks |
| `smart_lp_leaderboard` | Wallets ranked by what their closed LP positions made versus holding the tokens |
| `my_account` | With a key: your plan, subscriptions and custom alert rules |

APRs come back as percent text (`"42.0%"`) so a model never misreads a fraction, and every pool links to its page on
lpsignal.app.

## Example prompts

- "Which USDC/WETH pool and price range paid the most after impermanent loss this week?"
- "Best stablecoin pools on Base to provide liquidity in right now?"
- "Backtest a ±5% range on the top Aerodrome pool over the last 14 days."
- "What would a full-range Uniswap v3 position in ETH/USDC have earned net of impermanent loss over 30 days?"
- "Any stablecoin depegs or liquidity outflows in the last 24 hours?"
- "How have past LPSignal opportunity signals actually performed?"

## Free and paid

| | Free (no key) | Basic / Pro key |
|---|---|---|
| Pool rankings, pair search, backtests, track record | ✓ | ✓ |
| Risk alerts (depeg, liquidity outflow) | live | live |
| Opportunity signals | 24 hours after they fire | live |
| Smart-LP leaderboard | top 10, addresses masked | full (Pro) |
| Your custom alert rules | — | ✓ |

[Plans and pricing](https://lpsignal.app/pricing).

## FAQ

**Is there an MCP server for Uniswap liquidity pools?** Yes: this one covers Uniswap v3 and v4 (plus PancakeSwap v3,
Aerodrome and Velodrome Slipstream) on seven chains, with backtested net APR per price range.

**How do I calculate impermanent loss with an AI agent?** Connect this server and ask for a backtest
(`backtest_range`): it returns fee APR, impermanent loss and net APR of that exact range over the last 1–30 days.

**Do I need an API key?** No. Without one you get the free view; a paid LPSignal key adds live signals and your own
alert rules.

**Can the agent add liquidity for me?** No. Every tool is read-only. LPSignal's website lets you add liquidity from your
own wallet on the DEX's official contract.

**Is this financial advice?** No. Figures are backtests of past on-chain data, not forecasts. A range that paid well can
lose money if the price moves out of it.

## Related

- REST API: [OpenAPI 3.1](https://api.lpsignal.app/v1/openapi.json) · [documentation](https://lpsignal.app/docs) · [how net APR is measured](https://lpsignal.app/methodology)
- SDKs: [Node.js](https://github.com/LPSignals/lpsignal-node) (`npm i lpsignal`) · [Python](https://github.com/LPSignals/lpsignal-python) (`pip install lpsignal`)
- Live push: WebSocket stream, signed webhooks, Telegram
- Website: [lpsignal.app](https://lpsignal.app) — pool pages, [token pairs](https://lpsignal.app/pairs), [LP returns report](https://lpsignal.app/reports/lp-returns)

## 中文

**LPSignal MCP 服务**：Uniswap / DeFi 流动性池的 MCP 服务器，托管在 `https://api.lpsignal.app/v1/mcp`（Streamable HTTP），无需安装。
让 Claude、Cursor 等 AI agent 直接查询集中流动性池的**净 APR（手续费 − 无常损失）**排名、某个交易对在各链各费率的池子、任意价格区间的回测、
最新 LP 信号、信号历史表现和聪明 LP 排行，支持 Ethereum、Base、Arbitrum、BNB Chain、Optimism、Polygon、Robinhood Chain 上的
Uniswap v3/v4、PancakeSwap v3、Aerodrome、Velodrome。全部工具只读，不碰资金。不带密钥为免费视图；在请求头加
`Authorization: Bearer lps_…`（[账户页](https://lpsignal.app/zh/account)生成）按你的套餐返回。数据是历史回测，不是预测，也不构成投资建议。

## License

MIT
