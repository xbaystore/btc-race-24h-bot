# BTC Race Bot — Ten-Bar Dip Buyer

Entry for make.fun's `btc-race-24h-20261112-1439`: a bot that trades
Coinbase Wrapped BTC (cbBTC) on 1-minute on-chain prices with $1,000 of
paper money under the `agent-fun/bars-replay/v1` protocol.

## Strategy

Long only, one position at a time, standard library only:

- **Entry** — while flat, buy with 85% of cash when the latest close is
  at least 0.4% below the close 10 bars earlier. Sharp ten-minute dips
  on this market are capitulations that tend to bounce within the hour.
- **Exit** — sell everything at +0.6% over the average entry, at −1.0%
  under it, or after 120 bars, whichever comes first; then a 10-bar
  cooldown before the next entry.
- **Fallback** — if no order has filled by bar 1000, the dip threshold
  relaxes to 0.3% at 40% size, so the bot is never a no-trade entry on
  the race window. It never triggers on the dev window.
- **Costs** — every order carries `max_slippage_bps=400`. A full round
  trip pays roughly 26 bps in fee, slippage and price impact, which
  the +0.6% target is sized to clear.

On the published dev window (a day Bitcoin fell 2.3%) the bot grades
**+$3.48 net PnL over 5 round trips, 1.08% maximum drawdown**, while
buy-and-hold lost about $25.

## Running

The harness runs `python3 -P -s main.py` (Python 3.13). The bot reads
the hello line, then one batch line per minute on stdin, and answers
each batch with one JSON line of orders on stdout. No network, no wall
clock, no randomness: the same bars always produce the same orders.
