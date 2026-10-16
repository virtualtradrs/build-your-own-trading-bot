# Your first backtest — a procedure, not a promise

A backtest is a measurement, and like any measurement it is only as good as the assumptions
printed next to it. This page is the procedure we would follow for a first honest run. It assumes
you have finished stages 1–4 of the [roadmap](../README.md) or are working through them.

There is no finished backtester in this repository — that is the point of the exercise. Write it
yourself, in about a hundred lines, before reaching for a framework. Frameworks are faster once
you already know what you expect to see.

## Step 1 — Write the rule down before you touch data

One sentence, no hedging:

> Go long at the close of the day after the 20-day moving average crosses above the 100-day
> moving average; go flat at the close of the day after it crosses back below.

If you cannot write it in one sentence, you are not ready to code it. Vague rules ("when momentum
looks strong") cannot be tested, and testing them produces results that mean nothing.

## Step 2 — Get data you can re-obtain

Use an official endpoint from [stage 3](../README.md#stage-3--market-data-without-fooling-yourself)
and save the raw response before you clean it. Record four things with the file: the venue, the
symbol, the endpoint, and when you fetched it. A result you cannot re-fetch is an anecdote.

Check two obvious things before you trust the series: are the candles contiguous (no missing days),
and does the daily close of one venue roughly agree with another venue on the same days?

## Step 3 — Decide the signal timing, in writing

The most common beginner backtest bug is using the close of day *t* to fill at the close of day *t*.
On a daily candle you cannot act on a close you have not seen yet.

Pick one and write it down:

- **Signal from closes up to and including day t; position applies from t+1, filled at the close of t+1.**
  (Conservative, and what we would use for a first run.)
- Signal from day t, filled at the open of t+1. (Also defensible; be consistent.)

Then write the warm-up rule too: the first 100 rows of a 20/100 crossover backtest cannot produce
a signal, so they must be excluded from the measurement, not silently included as "flat".

## Step 4 — Charge costs on every fill

Fees and slippage are not optional decorations. Charge them on entry **and** exit, on the executed
notional, with no exceptions for "good" trades:

```python
def net_return_pct(entry_price, exit_price, cost_pct_per_fill=0.15):
    """Return between two prices, with fee + slippage charged on both fills.

    cost_pct_per_fill = fee % + slippage % charged on each side of the trade
    (e.g. 0.10 % fee + 0.05 % slippage = 0.15 % per fill).
    """
    entry_eff = entry_price * (1 + cost_pct_per_fill / 100)   # you pay a little more
    exit_eff = exit_price * (1 - cost_pct_per_fill / 100)     # you receive a little less
    return 100 * (exit_eff / entry_eff - 1)
```

Note what this converts a two-week trade into: a strategy that trades often needs to beat its own
costs before it beats anything else. Run your backtest twice, once with costs and once without, and
report the gap. If the gap is large, that *is* the result.

## Step 5 — Compare against something, or the number is meaningless

Every result needs a baseline on the same window, same asset, same costs. For a long-only crypto
strategy the honest baseline is **buy and hold over exactly the same period**: buy at the same
moment your first signal would have filled, hold to the same end date.

Report both, side by side, and include the maximum drawdown — not just the return. A strategy that
"beat" holding while drawing down 50% has not beaten holding in any sense that matters to sleep.

## Step 6 — Record the assumptions with the result

Save a small file next to your result, in plain JSON, and never edit a past result — add a new one.
The fields that matter:

```json
{
  "strategy": "20/100 SMA crossover, long-only, flat otherwise",
  "symbol": "<venue>:<pair>",
  "data_file": "<path or URL>",
  "data_rows": 0,
  "window": {"start": "YYYY-MM-DD", "end": "YYYY-MM-DD"},
  "signal_timing": "signal from closes up to and including t; filled at close of t+1",
  "costs": {"fee_pct_per_side": 0.10, "slippage_pct_per_side": 0.05},
  "net_of_costs": true,
  "result": {"strategy_return_pct": null, "baseline_return_pct": null, "max_drawdown_pct": null}
}
```

`net_of_costs: true` should be the boring default, and the nulls should be filled from the run,
never typed by hand from memory.

## The five questions to ask of any backtest you read

1. Where did the data come from, and could I fetch the same rows again?
2. Is the signal timing possible in real life, or does it use a price before it existed?
3. Are costs included, on both sides, on every fill?
4. What is the benchmark, on the same window with the same costs?
5. How many parameter sets were tried before this one was shown?

If a backtest cannot answer all five, treat it as a hypothesis rather than a measurement. And
remember that even a complete answer only describes the past: see
[The Probability of Backtest Overfitting](https://www.davidhbailey.com/dhbpapers/backtest-prob.pdf)
(stage 4) and [The Deflated Sharpe Ratio](https://www.davidhbailey.com/dhbpapers/deflated-sharpe.pdf)
(stage 5) for why a single good-looking result is weak evidence.

## A worked example from this team

We publish one of these files for real, on real data, in
[global-btc-nodes/results](https://github.com/virtualtradrs/global-btc-nodes/tree/main/results) —
including the JSON above and the equity curve plot. It is worth reading less for the numbers than
for the shape: a momentum strategy that lost money, published anyway, because "lost less than
buying and holding over this one window" was the honest finding.
