# Build Your Own Trading Bot — the open, curated roadmap

A curated learning path for building an algorithmic trading bot from zero to paper trading:
eight stages, each with a short explanation of what you are learning and the free resources we
would actually point someone at — mostly official documentation and primary sources, rather than
second-hand summaries. It is a reading-and-building guide, not a course with a certificate.

**This is not a bot you can run today.** There is no code to clone and start in this repository:
it is a roadmap and a resource collection, curated by hand. The bot is what *you* build along the
way, and the path ends at paper trading, not at live money.

New to the project? Read the [roadmap table](#the-roadmap-at-a-glance), then work down the stages.
If you want one concrete exercise before stage 1, try
[docs/YOUR-FIRST-BACKTEST.md](docs/YOUR-FIRST-BACKTEST.md).

Every external link below was loaded and confirmed working before it was added (last full check:
2026-10-15), and links are re-checked rather than left to rot. If you find a dead one, a PR that
fixes it is the most useful contribution there is — see [CONTRIBUTING.md](CONTRIBUTING.md).

## The roadmap at a glance

| # | Stage | What it gets you | Typical time |
|---|-------|------------------|--------------|
| 1 | [Markets & trading fundamentals](#stage-1--markets--trading-fundamentals) | Understand orders, spreads, liquidity and fees before writing a line of code | ~1 week |
| 2 | [Python & data tooling](#stage-2--python--data-tooling) | Read data, compute, plot, test — the working toolkit | ~2 weeks |
| 3 | [Market data](#stage-3--market-data-without-fooling-yourself) | Pull real candles from official APIs and store them reproducibly | ~1–2 weeks |
| 4 | [Strategy research & indicators](#stage-4--strategy-research--indicators) | Turn an idea into a precise, testable rule | ~2–3 weeks |
| 5 | [Backtesting with honest costs](#stage-5--backtesting-with-honest-costs) | Measure a strategy without lying to yourself | ~2–3 weeks |
| 6 | [Risk & position sizing](#stage-6--risk--position-sizing) | Decide how much to risk, and when a result is just luck | ~1–2 weeks |
| 7 | [Execution: paper trading first](#stage-7--execution-paper-trading-first) | Place orders in a sandbox, with keys that cannot move money | ~2–3 weeks |
| 8 | [Operations](#stage-8--operations-scheduling-monitoring-containers) | Keep it running: schedules, logs, alerts, containers | ~2 weeks |

Times are typical for someone coding a few evenings a week. They are estimates, not guarantees —
stage 4 and stage 5 are where most people slow down, and that is normal.

## Stage 1 — Markets & trading fundamentals

**What you will learn:** how a trade actually happens — who is on the other side, what an order
book is, where your money leaks away in spread and fees, and why leverage kills accounts.

- <https://www.kraken.com/learn> — Kraken's free education section: exchange basics, order types, fees.
- <https://en.wikipedia.org/wiki/Order_(exchange)> — the vocabulary of orders (market, limit, stop).
- <https://en.wikipedia.org/wiki/Bid%E2%80%93ask_spread> — the spread: the first cost every strategy pays.
- <https://en.wikipedia.org/wiki/Limit_order_book> — how resting orders become price.
- <https://en.wikipedia.org/wiki/Market_microstructure> — the field that studies all of the above.
- <https://en.wikipedia.org/wiki/Slippage_(finance)> — why your fill is worse than the price you saw.
- <https://en.wikipedia.org/wiki/Algorithmic_trading> — what "algorithmic" covers, and what it does not.
- <https://www.bis.org/publ/qtrpdf/r_qt1803e.htm> — Bank for International Settlements quarterly
  review on crypto market structure (longer read, worth it).
- <https://www.cftc.gov/LearnAndProtect/AdvisoriesAndArticles/fraudadv_forex.html> — a regulator's
  warning list about leveraged-product claims. Read it early; it is the best free defence against
  the marketing you will meet next.

**Done when:** you can explain, in two sentences each, what happens to your order when you send a
market buy into a thin book, and what a maker/taker fee does to a strategy that trades often.

## Stage 2 — Python & data tooling

**What you will learn:** the minimum Python you need to fetch, clean, compute on, plot and test
time-series data — and the habits that keep a project alive past week two.

- <https://docs.python.org/3/tutorial/> — the official tutorial. If Python is new, start here and
  finish it.
- <https://pandas.pydata.org/docs/user_guide/10min.html> — pandas in 10 minutes: the core of price data work.
- <https://pandas.pydata.org/docs/user_guide/timeseries.html> — dates, timezones and resampling.
- <https://pandas.pydata.org/docs/user_guide/groupby.html> — split-apply-combine; you will use it constantly.
- <https://numpy.org/doc/stable/user/absolute_beginners.html> — arrays and vectorised maths.
- <https://matplotlib.org/stable/users/explain/quick_start.html> — plot your equity curve; look at it.
- <https://requests.readthedocs.io/en/latest/> — calling HTTP APIs.
- <https://docs.python.org/3/library/venv.html> — one virtual environment per project.
- <https://docs.astral.sh/uv/> — faster environment and dependency management (optional, if venv feels slow).
- <https://jupyter.org/> — notebooks for exploring; keep the finished logic in `.py` files.
- <https://docs.pytest.org/en/stable/> — tests for your maths. Your backtester will need them.
- <https://peps.python.org/pep-0008/> and <https://peps.python.org/pep-0020/> — style and the Zen of Python.

**Done when:** you can load a CSV, resample it, compute a rolling statistic, plot it, and have a
test that fails loudly if the maths is wrong.

## Stage 3 — Market data (without fooling yourself)

**What you will learn:** where real price data comes from, how each exchange formats it, and how to
store it so a result you get today can be reproduced next month.

**Official exchange and data APIs (the primary sources):**

- <https://docs.kraken.com/api/> — Kraken REST API reference.
- <https://docs.kraken.com/api/docs/rest-api/get-ohlc-data> — Kraken OHLC candles endpoint, step by step.
- <https://docs.cdp.coinbase.com/exchange/docs/welcome> — Coinbase Exchange API docs.
- <https://binance-docs.github.io/apidocs/spot/en/> — Binance Spot API (REST + WebSocket).
- <https://www.okx.com/docs-v5/en/> — OKX v5 API; a good second example of a REST/WS design.
- <https://www.bitstamp.net/api/> — Bitstamp public and private endpoints.
- <https://docs.bitfinex.com/docs> — Bitfinex API docs.
- <https://docs.coingecko.com/reference/introduction> — CoinGecko API for reference prices and metadata.
- <https://www.cryptodatadownload.com/data/> — downloadable historical candle CSVs, useful as a cross-check.
- <https://mempool.space/docs/api> — public Bitcoin network data (blocks, fees, mempool): on-chain context,
  not price.

**Hygiene: making the data trustworthy.**

- <https://dvc.org/doc> — data version control: track dataset versions without committing giant files.
- <https://git-lfs.com/> — Git LFS, the simpler option for large binaries.
- <https://duckdb.org/docs/> — query local CSVs/Parquet with SQL; handy once a year of candles accumulates.
- <https://12factor.net/> — config in the environment, not in code. Fewer hard-coded API keys.

**Practical notes.** Exchange timeframes mean different things across venues (a "1 day" candle
closes at different UTC times). Keep raw responses untouched, derive cleaned files from them, and
always record: source, retrieval time, symbol, and the exact endpoint. Never mix two venues' prices
in one series without saying so.

**Done when:** you have a script that fetches daily candles from at least two venues for the same
symbol, writes them to versioned files, and prints the dates where the two disagree.

## Stage 4 — Strategy research & indicators

**What you will learn:** how to state a trading idea as an unambiguous rule — entry, exit, size —
and the indicators people build such rules from. Read the maths, then implement it yourself once;
a library tells you *what* it computes, not what it hides.

- <https://en.wikipedia.org/wiki/Moving_average> — moving averages, the workhorse of trend rules.
- <https://en.wikipedia.org/wiki/Relative_strength_index> — RSI: momentum as a bounded oscillator.
- <https://en.wikipedia.org/wiki/MACD> — MACD and why crossover systems are the classic first strategy.
- <https://en.wikipedia.org/wiki/Bollinger_Bands> — volatility bands around a moving average.
- <https://en.wikipedia.org/wiki/Technical_analysis> — what technical analysis claims, and the
  criticisms of it. Both halves matter.
- <https://chartschool.stockcharts.com/> — ChartSchool: free, worked explanations of indicators
  and chart patterns.
- <https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html> — rolling windows;
  most indicators are one call away.
- <https://github.com/TA-Lib/ta-lib-python> — TA-Lib Python bindings, if you want battle-tested implementations.
- <https://en.wikipedia.org/wiki/Trading_strategy> — the vocabulary of strategy definition.
- <https://scikit-learn.org/stable/> — optional, for when you move past hand-written rules.
- <https://www.davidhbailey.com/dhbpapers/backtest-prob.pdf> — "The Probability of Backtest
  Overfitting" (Bailey, Borwein, López de Prado, Zhu; free PDF). Read it before you trust a result.

**Done when:** you can write your rule as an if-then sentence with no "usually" or "when it looks
strong" in it, and you can point at the exact line of code that implements each clause.

## Stage 5 — Backtesting with honest costs

**What you will learn:** how to run a strategy over historical data in a way that does not flatter
it — costs in, look-ahead out, and a comparison that is fair.

**Frameworks and tooling:**

- <https://github.com/kernc/backtesting.py> — small, readable, good first backtester.
- <https://www.backtrader.com/docu/> — Backtrader documentation; event-driven and well documented.
- <https://vectorbt.dev/> — vectorised backtesting; fast, useful for parameter sweeps.
- <https://www.quantconnect.com/docs/v2/writing-algorithms/getting-started> — QuantConnect's guide to
  writing an algorithm (the hosted platform is optional; the docs are a good read).
- <https://github.com/ranaroussi/quantstats> — performance and drawdown reports from a returns series.
- <https://github.com/quantopian/zipline> — Zipline, the framework behind the original Quantopian.

**Costs, timing and the traps:**

- <https://en.wikipedia.org/wiki/Backtesting> — plus its list of common pitfalls.
- <https://en.wikipedia.org/wiki/Slippage_(finance)> — model it, do not ignore it.
- <https://en.wikipedia.org/wiki/Overfitting> — the single most common way a backtest lies.
- <https://www.davidhbailey.com/dhbpapers/deflated-sharpe.pdf> — "The Deflated Sharpe Ratio": why a
  high Sharpe from many trials means little.

**Rules that make a backtest worth reading.** Charge a fee *and* slippage on every fill, both
entry and exit. Decide the signal timing explicitly (a signal computed from today's close cannot
fill at today's close when you trade it live). Report the benchmark you are compared against, and
report costs separately from gross performance. If you tried twenty parameter sets, say so.

**Done when:** you can rerun your backtest and get the same numbers, and you can name the cost
assumptions it uses without opening the code.

## Stage 6 — Risk & position sizing

**What you will learn:** how to size a position and how to tell whether your good result was skill
or luck — the two questions that decide whether a strategy is worth running.

- <https://en.wikipedia.org/wiki/Financial_risk_management> — the vocabulary: exposure, limits, hedging.
- <https://en.wikipedia.org/wiki/Maximum_drawdown> and
  <https://en.wikipedia.org/wiki/Drawdown_(economics)> — the number that actually ends accounts.
- <https://en.wikipedia.org/wiki/Volatility_(finance)> — measuring how much things move.
- <https://en.wikipedia.org/wiki/Value_at_risk> — a standard risk measure and its blind spots.
- <https://en.wikipedia.org/wiki/Sharpe_ratio> — risk-adjusted return, and its limitations.
- <https://en.wikipedia.org/wiki/Kelly_criterion> — optimal sizing in theory; in practice people
  use a fraction of it. Understand why.
- <https://en.wikipedia.org/wiki/Risk_of_ruin> — why bet sizing, not prediction, decides survival.
- <https://www.cftc.gov/LearnAndProtect/AdvisoriesAndArticles/fraudadv_forex.html> — regulator's
  take on leveraged losses (repeated from stage 1 on purpose).

**Done when:** your strategy has a written maximum position size, a stop or an exit rule, and a
sentence stating what would make you switch it off.

## Stage 7 — Execution: paper trading first

**What you will learn:** how to send orders through a real API with fake money, and how to survive
the gap between "it worked in the backtest" and "it works now".

**Sandbox / paper environments first:**

- <https://docs.alpaca.markets/docs/paper-trading> — Alpaca paper trading: a broker API with a real
  paper account. The gentlest place to start placing orders.
- <https://docs.alpaca.markets/docs/orders-at-alpaca> — order lifecycle: what states an order passes through.
- <https://github.com/alpacahq/alpaca-py> — the official Python SDK.
- <https://docs.cdp.coinbase.com/exchange/docs/sandbox> — Coinbase Exchange sandbox environment.
- <https://binance-docs.github.io/apidocs/spot/en/> — the same Binance Spot docs include testnet setup
  (section "Testnet"), for crypto order types in a fake-money environment.
- <https://github.com/freqtrade/freqtrade> — a mature open-source bot; read its repo as a worked example
  of exchange integration, config and dry-run mode.

**Connecting to many exchanges with one interface:**

- <https://github.com/ccxt/ccxt> — CCXT: one library, many exchange APIs.
- <https://docs.ccxt.com/> — CCXT documentation.

**Habits that keep a live bot alive.** Create API keys with trading-only permissions — never
withdrawal permissions. Reconcile your internal state against the exchange every start-up —
assume it is wrong.
Make order submission idempotent so a retry cannot double your position. Rate-limit yourself before
the exchange does, and treat every error message as a case you must handle.

**Done when:** your strategy has run for at least two weeks in a paper account and you have compared
the live behaviour against the backtest's assumptions, cost by cost.

## Stage 8 — Operations: scheduling, monitoring, containers

**What you will learn:** how to run the thing unattended — on a schedule, with logs you can read,
alerts when it breaks, and secrets that stay secret.

- <https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows> — cron schedules
  in GitHub Actions (how this team's own daily pipeline runs).
- <https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions> — keeping API keys out of code.
- <https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/monitoring-workflows> —
  seeing whether a scheduled job actually ran.
- <https://crontab.guru/> — check any cron expression in plain English.
- <https://docs.docker.com/get-started/> — containers, from zero.
- <https://docs.docker.com/compose/> — run a bot plus its database with one command.
- <https://docs.python.org/3/library/logging.html> — structured logging that you can search later.
- <https://prometheus.io/docs/introduction/overview/> — metrics and alerting, when logs are not enough.
- <https://grafana.com/docs/> — dashboards for those metrics.
- <https://12factor.net/> — config, logs and process discipline in one short read.
- <https://github.com/theskumar/python-dotenv> — loading local secrets during development, without committing them.

**Done when:** your bot runs on a schedule you did not start by hand, writes a log you can find, and
tells you when it fails instead of silently stopping.

## Our own open-source project

This roadmap is maintained by the same team that publishes
**[virtualtradrs/global-btc-nodes](https://github.com/virtualtradrs/global-btc-nodes)** — an
open-source Bitcoin network + market data toolkit. A scheduled GitHub Actions pipeline fetches real
BTC-USD candles plus mempool, fee and reachable-node telemetry and commits them in-repo, so a
clone gives you the same dataset the docs were written against. It is a worked, end-to-end example
of stage 3 (data) — fetch, validate, version, publish — and its `results/` directory shows the
stage 5 habit we recommend: a momentum SMA 20/100 crossover strategy returned **-8.87%** against buy-and-hold's
**-20.79%** over **2025-01-30 → 2026-10-13**, net of a **0.10% fee + 0.05% slippage per side**.
Both strategies lost money — the honest reading is that the crossover rule lost less than simply
holding, over one window, on one symbol. Nothing there is a recommendation, and a single favourable
comparison proves far less than it looks like.

## Contributing

Additions are welcome and should keep the shape of this repository: one stage, one purpose.
See [CONTRIBUTING.md](CONTRIBUTING.md) for the three rules that matter — the link must work, it
must be honest, and it must be free to read (or clearly labelled otherwise).

## License

MIT — see [LICENSE](LICENSE). Copyright (c) 2026 Virtual Traders. You are free to use, adapt and
redistribute this roadmap; a link back is appreciated, not required.

## Disclaimer

This repository is educational material, not financial advice, and not a recommendation to trade
or to use any of the resources listed. Trading — and especially leveraged trading — can lose you
more than you put in. Nothing here is audited, guaranteed to be current, or suitable for your
circumstances. If you build something from this roadmap, test it with money you can afford to lose,
in paper trading first, and only then consider anything else. You are responsible for your own
results and for complying with the rules that apply where you live.
