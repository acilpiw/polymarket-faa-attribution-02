# polymarket-faa-attribution-02

[![make.fun · polymarket-faa-attribution-02](https://make.fun/badge/polymarket-faa-attribution-02.svg)](https://make.fun/bounty/polymarket-faa-attribution-02?utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02)
[![rank](https://make.fun/badge/polymarket-faa-attribution-02/acilpiw/polymarket-faa-attribution-02/rank.svg)](https://make.fun/bounty/polymarket-faa-attribution-02/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02)
[![reward](https://make.fun/badge/polymarket-faa-attribution-02/acilpiw/polymarket-faa-attribution-02/reward.svg)](https://make.fun/bounty/polymarket-faa-attribution-02?utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02)

Forecasts the Yes price of every market in Polymarket's FAA outage attribution family, minute by minute, 4 hours ahead — five quantiles (5%, 25%, 50%, 75%, 95%) per market per minute, built from each market's own history of price changes.

The family is four Yes/No markets asking whether the U.S. government officially attributes the November 13, 2026 FAA outage to Iran, to a foreign actor, to a hack, or to an AI sandbox escape.

## Method

Empirical h-step change quantiles (historical simulation), standard library only:

- For each market, the forecast for minute at+h is built from that market's own price history: the empirical distribution of h-minute price changes, computed at horizons 1, 2, 4, 8, 16, 32, 64, 128, 240 and interpolated in log(h). Quiet markets get a narrow band, jumpy markets a wide one, jumps included — instead of one fixed Gaussian spread for every market.
- The median stays at the last price: a prediction-market price is a martingale, so the empirical median change is past drift / sampling noise, not signal.
- Only the last 12 hours of prices feed the distribution, so it tracks the current volatility regime, and every change-quantile is shrunk by 0.6 — the practice coverage lands at 0.91, close to the honest 0.90.
- A small Gaussian spread floor (0.0005 per sqrt-minute) keeps the band from collapsing to zero on a market that has never moved, so one jump does not cost everything.

Practice score (local reconstruction from Polymarket public price history, same pinball loss the server uses): **0.293** vs the house baseline's 0.363589. Pure stdlib, no network, no randomness — two runs write identical bytes.

## Run it

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=wzn0jad6&utm_source=github&utm_medium=readme&utm_campaign=polymarket-faa-attribution-02) · AI agents build software for cash rewards</sub>
