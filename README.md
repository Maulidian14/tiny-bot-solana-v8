# Tiny Bot v8 — Oversold Dip Buyer

[![make.fun](https://img.shields.io/badge/make.fun-Tiny%20Bot-blue)](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![dev PnL](https://img.shields.io/badge/dev%20PnL-%2B%24262.39-brightgreen)](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![prize](https://img.shields.io/badge/prize-%242%20pot-gold)](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)

A tiny paper-trading bot for [Tiny Bot, Easy $2: Beat Buy-and-Hold on Solana](https://make.fun/c/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1) (make.fun, `agent-fun/bars-replay/v1`). It reads hourly Solana DEX bars and decides when to hold and when to sit in USDC — long only, no shorts, no leverage.

## Strategy: buy oversold dips, exit fast, otherwise sit in cash

Live SOL on 25 Nov 2026: $67.6, −4.3% in 24h, at its 30-day low, −33% in 30 days — a grinding downtrend with ~1% hourly volatility. The final window (27–30 Nov, collected after the deadline) is most likely more of the same. Buy-and-hold lost −8.7% on the dev window and every house baseline lost too, because trend followers buy relief rallies that fail. The edge here is the opposite:

- **Entry:** RSI(14) < 30 (oversold), or the close fell >4% over 6 bars (sharp dip).
- **Exit:** RSI(14) > 50, or −2.5% stop, or +3.5% take profit. All level-based, so a refused order is retried while it still holds — and entries have **no cooldown**: after a stop the bot re-buys the same dip next bar if it is still oversold. Backtested, any re-entry delay destroys the edge (a 1-bar cooldown costs $91, a 12-bar one $463 on the dev window).
- **Sizing:** cash split evenly over the symbols entering this batch, each capped at 25% of equity; a sell's proceeds never fund a buy of the same reply (entries after a sell batch go out on the next batch).
- **Deterministic:** float64 RSI only, universe order, no clock, environment or randomness — two runs produce byte-identical action logs.

## Results (dev window, `grade-local --driver process`)

| Entry | Net PnL | Trades | Max drawdown |
|---|---|---|---|
| **Tiny Bot v8** | **+$262.39** | 44 | 3.9% |
| Best house baseline (macd-cci) | −$20.22 | 17 | 2.5% |
| Buy-and-hold | −$869.34 | — | 11.0% |
| Hold cash | $0.00 | 0 | 0% |

Rolling 72-bar windows (the final window's length): avg +$136.20, beats buy-and-hold 17/17, ≥8 trades in 17/17 — clears the minimum-trades guardrail with room.

## Run it

```sh
node main.ts   # speaks agent-fun/bars-replay/v1 on stdin/stdout
```

`vendor/makefun-bars.ts` is a byte-identical copy of the kit's `sdk/makefun-bars.ts`. The bot ships everything it runs; it reads no network, no environment secrets, and no files outside this repository.

## License

MIT — see [LICENSE](LICENSE).
