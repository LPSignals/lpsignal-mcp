---
name: lpsignal-lp
description: Answer questions about concentrated-liquidity LP pools (Uniswap v3/v4, PancakeSwap v3, Aerodrome/Velodrome) with the LPSignal MCP tools — which pool and price range paid the most after impermanent loss, backtests of a range, LP opportunity signals, stablecoin depeg and liquidity-outflow alerts, and the smart-LP leaderboard. Use when the user asks about LP yields, net APR, impermanent loss, pool rankings or LPSignal signals.
---

# LPSignal: LP pools by net APR

The `lpsignal` MCP server that comes with this plugin ranks concentrated-liquidity pools by **net APR = fees earned by
a specific price range minus its impermanent loss**, from hourly backtests on exact on-chain fees. It only reads: it
cannot trade, sign, move funds, change settings or create alert rules. Send the user to https://lpsignal.app for those.

## Ground rules

- Every figure is annualised and comes from a backtest of past data: history, not a forecast or financial advice.
  Say so when you recommend anything.
- Report what the tools return. Rankings, APRs, plans and prices change: take them from the tools, never from memory.
- Use on-chain symbols: `WETH` (not ETH), `WBTC` or `cbBTC` (not BTC).
- Link every pool you mention as given in the tool output (`https://lpsignal.app/pools/<chain>/<address>`).
- Weigh `tvlUsd`: rankings are by net APR, so a small pool can come first.
- Each signal from `list_signals` says `access: live` or `delayed_24h`. Never present a delayed one as live.

## Access

The first tool call opens a browser sign-in: the user signs in on lpsignal.app with their wallet and clicks **Allow**
(if it does not open, run `/mcp` and authenticate `lpsignal`). A free account sees opportunity signals 24 hours late;
Basic and Pro see them live; Pro sees the full smart-LP leaderboard. Plans: https://lpsignal.app/pricing — point the
user there, never buy for them.

## Which tool

| Question | Tool |
|---|---|
| Best pools on a chain or of a class (stable, correlated, volatile) | `list_pools` |
| Every pool of one pair, e.g. USDC + WETH | `find_pair_pools` |
| One pool's ranges over 1 h / 24 h / 7 d / 30 d | `get_pool` |
| How a ±x% (or full) range would have done over 1–30 days | `backtest_range` |
| Latest opportunities and risk alerts | `list_signals` (page back with `before`) |
| How past signals really did | `signal_track_record` |
| Wallets that LP best | `smart_lp_leaderboard` |
| The user's plan, push subscriptions, alert rules | `my_account` |

## Typical flow

1. Pair question → `find_pair_pools`; general question → `list_pools` with a chain or class.
2. Open the candidates with `get_pool` to compare windows (a 7-day winner that is negative over 24 h is fading).
3. Test the width the user has in mind with `backtest_range`.
4. Before calling a signal good, check `signal_track_record` for that kind: the median realised APR is usually far
   below the APR at the signal.
