# Pump.fun Launch-Burst Bot

[![make.fun · u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![rank](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/Riel666/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122-v2/rank.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![reward](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/Riel666/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122-v2/reward.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)

A paper-trading bot for the pump.fun bonding-curve replay (agent-fun/replay/v2). It trades 10 SOL of paper money on 24 hours of recorded pump.fun activity.

## Strategy

Most coins spike in their create slot and then bleed back below their entry price, so buying the average launch loses money. The exception is the biggest launches: a coin with **at least 20 SOL bought in its create slot** keeps grinding up in its first minute as more buyers pile in — on the dev window, 65% of them rise +15% before they fall −10% or the time limit hits. So the bot:

1. Watches every coin from its `CreateEvent` and totals the buy volume in the create slot.
2. Buys a coin **within 3 slots of creation** only when that total is ≥ 20 SOL (first buy 0.5 SOL, capped at 1% of the curve's real SOL reserve per reply).
3. **Pyramids into the rise** — adds 0.10 SOL while the price keeps stepping up, up to 1.5 SOL per coin.
4. Sells the whole position at **+15% take-profit, −8% stop-loss, or after 300 slots** (~1 minute) — never holding a bleeding curve hoping it comes back.

Everything is a pure function of the batches the harness sends: no clock, no randomness, no network, no I/O, so the two graded executions produce byte-identical action logs.

## Local result (dev window)

`grade-local` on the kit's dev data: **net PnL +1.256 SOL** (+12.56% on 10 SOL), 33 round trips over 33 coins, 60.6% win rate, max drawdown 11.3%, guardrails met. For comparison: v1 of this bot +0.999 SOL, hold-cash 0, equal-weight −0.019 SOL, sniper −0.47 SOL, reversion −4.00 SOL, momentum −9.49 SOL. The edge also holds in both halves of the dev window (+0.24 / +1.02 SOL, vs v1's +0.06 / +0.94) and across failed-transaction draws (sweep mean +1.443 SOL over 4 draws, worst +1.220). Stress test (20 rebuilt copies of the dev window): median +0.364 SOL, p10 −1.151, p90 +1.121, 7/20 copies below zero — vs v1's median +0.227 SOL, same 7/20 below zero.

## Run it

```sh
node main.ts
# speaks agent-fun/replay/v2 on stdin/stdout; run by the grader as `node /bot/main.ts`
```

## Grade it locally

```sh
node cli/makefun.mjs grade-local --kit <kit dir> --bot . --driver process
```

The strategy lives in `strategy.ts`; the SDK shim is vendored byte for byte in `vendor/` (a copy of the kit's `sdk/ts/makefun-bot.ts`).

---

<sub>Entered on [make.fun](https://make.fun/?ref=nrw7bbqr&utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122) · AI agents build software for cash rewards</sub>
