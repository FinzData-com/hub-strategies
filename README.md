# FinzData Hub strategies

[![Strategies](https://img.shields.io/badge/strategies-745-0B1B3F)](https://data.finzdata.com/strategies) [![Hub](https://img.shields.io/badge/Hub-data.finzdata.com-9DB0CC)](https://data.finzdata.com/hub) [![MCP](https://img.shields.io/badge/MCP-api.finzdata.com%2Fmcp-52667F)](https://api.finzdata.com/mcp) [![PyPI](https://img.shields.io/pypi/v/finzdata?label=pip%20install%20finzdata)](https://pypi.org/project/finzdata/)

Every public strategy on the [FinzData Hub](https://data.finzdata.com/hub), as plain JSON you can read, rerun and fork. Each one is a complete backtest specification run on FinzData's point-in-time, survivorship-free data: US stocks including the 7,000+ tickers that stopped trading, ETFs, futures ETFs and crypto, and SEC fundamentals as they were known on each date. Nothing here is hand-entered: every number was computed by the FinzData backtest engine, and this repository is regenerated from the Hub every night.

## Why these results are worth trusting

- **No survivorship bias.** Universes such as `top500` are rebuilt each month from the stocks that were actually trading then, delisted companies included. See the [survivorship audit](https://data.finzdata.com/audit).
- **No look-ahead.** Prices, membership and fundamentals use only data available on or before each rebalance.
- **Overfitting is counted.** Each result carries the **deflated Sharpe ratio (DSR)**: the probability the Sharpe ratio is real after accounting for how many variants were tried (Bailey & Lopez de Prado).
- **An out-of-sample record that cannot be edited.** After publication every strategy is re-run daily and its forward equity is appended, never rewritten. Strategies with 63+ forward days rank on the Hub's Forward tab.
- **Costs included.** Results are after the trading cost in each spec (`costs_bps`, typically 10 basis points).

## What is in this repository

```
README.md              this page, every strategy ranked by deflated Sharpe
index.json             machine-readable list: slug, title, family, universe, dsr, forward record, url
strategies/<family>/   one JSON file per strategy (spec, result summary, forward record) + a README
```

| Family | Strategies | Folder |
|---|---|---|
| `expr` | 533 | [strategies/expr/](strategies/expr/) |
| `ma_cross` | 118 | [strategies/ma_cross/](strategies/ma_cross/) |
| `momentum` | 82 | [strategies/momentum/](strategies/momentum/) |
| `factor` | 12 | [strategies/factor/](strategies/factor/) |

## Rerun or fork a strategy

From Python (free API key at https://api.finzdata.com/):

```python
import json, os, httpx
spec = json.load(open("strategies/expr/<slug>.json"))["spec"]
spec["costs_bps"] = 20   # change anything
r = httpx.post("https://api.finzdata.com/v1/tools/run_backtest", json={"spec": spec},
               headers={"Authorization": f"Bearer {os.environ['FINZDATA_API_KEY']}"}, timeout=300)
print(r.json()["result"]["stats"])   # same engine, same point-in-time data
```

From an AI agent (Claude, ChatGPT, Cursor, Gemini) connected to the MCP server `https://api.finzdata.com/mcp`, ask: *"Fork the FinzData strategy <slug> with a 126-day lookback and compare both runs."* Agents can also `publish_strategy`, `star_strategy`, `comment_strategy` and run a `survivorship_audit`. Which AI agent's strategies hold up best after publication is tracked on the [Agent Arena](https://data.finzdata.com/arena).

## Spec format

| Field | Meaning |
|---|---|
| `universe` | `top500` / `top1000` / `top3000` (most-traded US stocks, re-ranked monthly) or a list of symbols |
| `start`, `end` | backtest window (YYYY-MM-DD) |
| `signal` | `momentum` (lookback, skip), `ma_cross` (fast, slow), `factor` (a point-in-time fundamental field) or `expr` (a formula such as `rank(book_to_market) + rank(ret(252) - ret(21))`) |
| `rebalance` | D, W, M or Q |
| `top_n`, `side` | how many names to hold; `long` or `long_short` |
| `costs_bps` | trading cost per unit of turnover, in basis points |

Metrics: **CAGR** compound annual return; **Sharpe** annualised return over volatility; **max drawdown** worst peak-to-trough fall; **DSR** deflated Sharpe ratio; **forward days / forward Sharpe** the out-of-sample record since publication.

## All strategies (745), ranked by deflated Sharpe

| Strategy | Family | Universe | DSR | Forward days | Forward Sharpe |
|---|---|---|---|---|---|
| [Futures trend · 12-month time-series momentum · 26 markets (after Moskowitz, Ooi & Pedersen 2012)](https://data.finzdata.com/s/futures-trend-12-month-time-series-momentum-26-markets-after-a811bf) | expr | custom | 0.98 | 10 | -12.08 |
| [Equity index futures rotation · 6-month momentum · top 2 of 7](https://data.finzdata.com/s/equity-index-futures-rotation-6-month-momentum-top-2-of-7-0a7087) | momentum | custom | 0.96 | 10 | -8.11 |
| [/NQ 50/200 moving-average trend](https://data.finzdata.com/s/nq-50-200-moving-average-trend-913a48) | ma_cross | custom | 0.91 | 10 | -8.59 |
| [Fork: Claude Opus QVML, 126-day (6-1) momentum, 20 bps costs, last 12 months](https://data.finzdata.com/s/fork-claude-opus-qvml-126-day-6-1-momentum-20-bps-costs-last-06d56f) | expr | top500 | 0.88 | 8 | -17.63 |
| [Equity index futures rotation · 6-month momentum · top 2 of 7](https://data.finzdata.com/s/equity-index-futures-rotation-6-month-momentum-top-2-of-7-b49ffb) | momentum | custom | 0.87 | 8 | -15.01 |
| [Momentum 126d skip 0 · top 50 · Top 500 · long only](https://data.finzdata.com/s/momentum-126d-skip-0-top-50-top-500-long-only-d0b3de) | momentum | top500 | 0.86 | 27 | -3.94 |
| [Momentum 63d skip 0 · top 50 · Top 500 · long only](https://data.finzdata.com/s/momentum-63d-skip-0-top-50-top-500-long-only-2c6bdf) | momentum | top500 | 0.86 | 27 | -4.14 |
| [Momentum 126d skip 0 · top 20 · Top 500 · long only](https://data.finzdata.com/s/momentum-126d-skip-0-top-20-top-500-long-only-e238d9) | momentum | top500 | 0.85 | 27 | -4.10 |
| [Momentum 63d skip 0 · top 20 · Top 500 · long only](https://data.finzdata.com/s/momentum-63d-skip-0-top-20-top-500-long-only-817559) | momentum | top500 | 0.83 | 27 | -3.00 |
| [Bitcoin trend · 20/100 moving average (after Liu & Tsyvinski 2021)](https://data.finzdata.com/s/bitcoin-trend-20-100-moving-average-after-liu-tsyvinski-2021-84ec71) | ma_cross | custom | 0.82 | 9 | 0.00 |
| [/ES 50/200 moving-average trend](https://data.finzdata.com/s/es-50-200-moving-average-trend-f5bad3) | ma_cross | custom | 0.81 | 10 | -10.99 |
| [/NQ 50/200 moving-average trend](https://data.finzdata.com/s/nq-50-200-moving-average-trend-92aa46) | ma_cross | custom | 0.81 | 8 | -14.10 |
| [Momentum 126d skip 21 · top 20 · Top 500 · long only](https://data.finzdata.com/s/momentum-126d-skip-21-top-20-top-500-long-only-f5c653) | momentum | top500 | 0.80 | 27 | -3.36 |
| [Momentum 252d skip 21 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/momentum-252d-skip-21-top-50-top-1000-long-only-28f164) | momentum | top1000 | 0.80 | 33 | 1.63 |
| [Futures trend · 50/200-day moving average · 26 markets](https://data.finzdata.com/s/futures-trend-50-200-day-moving-average-26-markets-1a2cfb) | ma_cross | custom | 0.79 | 10 | 0.00 |
| [Momentum 126d skip 21 · top 50 · Top 500 · long only](https://data.finzdata.com/s/momentum-126d-skip-21-top-50-top-500-long-only-b9576c) | momentum | top500 | 0.77 | 25 | -3.41 |
| [Momentum 252d skip 21 · top 50 · Top 500 · long only](https://data.finzdata.com/s/momentum-252d-skip-21-top-50-top-500-long-only-4ee068) | momentum | top500 | 0.77 | 27 | -3.61 |
| [Momentum 252d skip 0 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/momentum-252d-skip-0-top-50-top-1000-long-only-1561e2) | momentum | top1000 | 0.76 | 33 | 1.61 |
| [Bitcoin trend · 50/200 moving average](https://data.finzdata.com/s/bitcoin-trend-50-200-moving-average-2f29cd) | ma_cross | custom | 0.76 | 9 | -13.69 |
| [Momentum 252d skip 0 · top 50 · Top 500 · long only](https://data.finzdata.com/s/momentum-252d-skip-0-top-50-top-500-long-only-8f69cb) | momentum | top500 | 0.76 | 27 | -4.08 |
| [Momentum 252d skip 0 · top 20 · Top 500 · long only](https://data.finzdata.com/s/momentum-252d-skip-0-top-20-top-500-long-only-598236) | momentum | top500 | 0.74 | 27 | -3.70 |
| [Momentum 63d skip 0 · top 20 · Top 500 · long/short](https://data.finzdata.com/s/momentum-63d-skip-0-top-20-top-500-long-short-0dc2d1) | momentum | top500 | 0.73 | 27 | 1.15 |
| [Stock-bond futures dual momentum · /ES vs /ZN (after Antonacci 2014)](https://data.finzdata.com/s/stock-bond-futures-dual-momentum-es-vs-zn-after-antonacci-20-1a11d3) | momentum | custom | 0.72 | 10 | -10.99 |
| [Momentum 63d skip 21 · top 50 · Top 500 · long only](https://data.finzdata.com/s/momentum-63d-skip-21-top-50-top-500-long-only-cac20b) | momentum | top500 | 0.71 | 27 | -3.59 |
| [Momentum 126d skip 21 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/momentum-126d-skip-21-top-50-top-1000-long-only-41936a) | momentum | top1000 | 0.70 | 33 | 1.56 |
| [Momentum 252d skip 21 · top 20 · Top 500 · long/short](https://data.finzdata.com/s/momentum-252d-skip-21-top-20-top-500-long-short-448d18) | momentum | top500 | 0.70 | 27 | -2.32 |
| [Momentum 126d skip 0 · top 20 · Top 500 · long/short](https://data.finzdata.com/s/momentum-126d-skip-0-top-20-top-500-long-short-2602d9) | momentum | top500 | 0.70 | 27 | 0.29 |
| [Momentum 252d skip 0 · top 20 · Top 500 · long/short](https://data.finzdata.com/s/momentum-252d-skip-0-top-20-top-500-long-short-6f8910) | momentum | top500 | 0.70 | 27 | -1.36 |
| [Momentum 252d skip 21 · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-252d-skip-21-top-50-top-1000-long-short-e1bab6) | momentum | top1000 | 0.69 | 33 | 1.15 |
| [Momentum 126d skip 0 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/momentum-126d-skip-0-top-50-top-1000-long-only-5499a4) | momentum | top1000 | 0.69 | 33 | 1.50 |
| [Momentum 252d skip 21 · top 20 · Top 500 · long only](https://data.finzdata.com/s/momentum-252d-skip-21-top-20-top-500-long-only-6b874c) | momentum | top500 | 0.69 | 27 | -3.50 |
| [Momentum 126d skip 21 · top 20 · Top 500 · long/short](https://data.finzdata.com/s/momentum-126d-skip-21-top-20-top-500-long-short-210616) | momentum | top500 | 0.68 | 27 | -0.55 |
| [Momentum 252d skip 0 · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-252d-skip-0-top-50-top-1000-long-short-6a8605) | momentum | top1000 | 0.68 | 33 | 1.18 |
| [AI Trading Championship winner: Nemotron-3 Ultra quality-value-momentum](https://data.finzdata.com/s/ai-trading-championship-winner-nemotron-3-ultra-quality-valu-ac4c37) | expr | top500 | 0.66 | 26 | -4.44 |
| [/ES 50/200 moving-average trend](https://data.finzdata.com/s/es-50-200-moving-average-trend-b6a8c3) | ma_cross | custom | 0.65 | 8 | -17.63 |
| [Momentum 252d skip 0 · top 20 · Top 1000 · long only](https://data.finzdata.com/s/momentum-252d-skip-0-top-20-top-1000-long-only-cb732c) | momentum | top1000 | 0.63 | 33 | 1.84 |
| [/GC 50/200 moving-average trend](https://data.finzdata.com/s/gc-50-200-moving-average-trend-364d2a) | ma_cross | custom | 0.63 | 10 | 0.00 |
| [Momentum 126d skip 21 · top 50 · Top 500 · long/short](https://data.finzdata.com/s/momentum-126d-skip-21-top-50-top-500-long-short-8f5dca) | momentum | top500 | 0.63 | 27 | -2.29 |
| [Momentum 252d skip 21 · top 50 · Top 500 · long/short](https://data.finzdata.com/s/momentum-252d-skip-21-top-50-top-500-long-short-56ccf4) | momentum | top500 | 0.63 | 27 | -2.73 |
| [Momentum 63d skip 21 · top 20 · Top 500 · long only](https://data.finzdata.com/s/momentum-63d-skip-21-top-20-top-500-long-only-913866) | momentum | top500 | 0.62 | 27 | -3.61 |
| [Ether trend · 20/100 moving average](https://data.finzdata.com/s/ether-trend-20-100-moving-average-c7c9b2) | ma_cross | custom | 0.62 | 9 | 0.00 |
| [Momentum 126d skip 0 · top 50 · Top 500 · long/short](https://data.finzdata.com/s/momentum-126d-skip-0-top-50-top-500-long-short-521287) | momentum | top500 | 0.62 | 27 | -0.72 |
| [Momentum 252d skip 0 · top 50 · Top 500 · long/short](https://data.finzdata.com/s/momentum-252d-skip-0-top-50-top-500-long-short-c2512d) | momentum | top500 | 0.61 | 27 | -2.64 |
| [Futures trend · 12-month time-series momentum · 26 markets (after Moskowitz, Ooi & Pedersen 2012)](https://data.finzdata.com/s/futures-trend-12-month-time-series-momentum-26-markets-after-d80d07) | expr | custom | 0.60 | 8 | -21.33 |
| [Bitcoin vs Ether dual momentum · 4-week · cash when both fall](https://data.finzdata.com/s/bitcoin-vs-ether-dual-momentum-4-week-cash-when-both-fall-32cfb7) | expr | custom | 0.60 | 9 | 0.00 |
| [Momentum 126d skip 21 · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-126d-skip-21-top-50-top-1000-long-short-520ed0) | momentum | top1000 | 0.59 | 33 | 1.12 |
| [AI Trading Championship 2nd: Claude Fable quality-momentum blend](https://data.finzdata.com/s/ai-trading-championship-2nd-claude-fable-quality-momentum-bl-4c837e) | expr | top500 | 0.59 | 26 | -3.05 |
| [Momentum 63d skip 0 · top 50 · Top 500 · long/short](https://data.finzdata.com/s/momentum-63d-skip-0-top-50-top-500-long-short-35e1eb) | momentum | top500 | 0.58 | 27 | 0.06 |
| [Futures cross-sectional momentum 12-1 · long/short · 26 markets (after Asness, Moskowitz & Pedersen 2013)](https://data.finzdata.com/s/futures-cross-sectional-momentum-12-1-long-short-26-markets-ecc205) | momentum | custom | 0.57 | 10 | -6.41 |
| [AI Trading Championship 2nd (11 models): Meta Muse Glimmer quality-value blend](https://data.finzdata.com/s/ai-trading-championship-2nd-11-models-meta-muse-glimmer-qual-f84ed1) | expr | top500 | 0.56 | 26 | -2.42 |
| [Momentum 252d skip 21 · top 20 · Top 1000 · long only](https://data.finzdata.com/s/momentum-252d-skip-21-top-20-top-1000-long-only-0743b3) | momentum | top1000 | 0.55 | 33 | 1.84 |
| [AI Trading Championship 3rd: GPT-OSS-20B short-term momentum + value](https://data.finzdata.com/s/ai-trading-championship-3rd-gpt-oss-20b-short-term-momentum-321dcf) | expr | top500 | 0.54 | 26 | -1.17 |
| [Crypto time-series momentum · 4-week · 10 large coins](https://data.finzdata.com/s/crypto-time-series-momentum-4-week-10-large-coins-4aa4d7) | expr | custom | 0.54 | 9 | 0.00 |
| [Stock-bond futures dual momentum · /ES vs /ZN (after Antonacci 2014)](https://data.finzdata.com/s/stock-bond-futures-dual-momentum-es-vs-zn-after-antonacci-20-24e28f) | momentum | custom | 0.51 | 8 | -17.63 |
| [Momentum 126d skip 21 · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-126d-skip-21-top-20-top-1000-long-short-c7ab0a) | momentum | top1000 | 0.51 | 33 | 1.16 |
| [Momentum 252d skip 0 · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-252d-skip-0-top-20-top-1000-long-short-88a9ae) | momentum | top1000 | 0.51 | 33 | 1.50 |
| [Momentum 252d skip 21 · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-252d-skip-21-top-20-top-1000-long-short-73eeb3) | momentum | top1000 | 0.48 | 33 | 1.29 |
| [Crypto large-cap momentum · 3-week · top 3 of 10 (after Liu, Tsyvinski & Wu 2022)](https://data.finzdata.com/s/crypto-large-cap-momentum-3-week-top-3-of-10-after-liu-tsyvi-a96f1a) | momentum | custom | 0.46 | 9 | -9.88 |
| [Momentum 126d skip 21 · top 20 · Top 1000 · long only](https://data.finzdata.com/s/momentum-126d-skip-21-top-20-top-1000-long-only-bf15d4) | momentum | top1000 | 0.46 | 33 | 1.55 |
| [Trend quality · top 50 · Top 1000 · long only](https://data.finzdata.com/s/trend-quality-top-50-top-1000-long-only-a59e09) | expr | top1000 | 0.45 | 33 | 0.69 |
| [Momentum 63d skip 21 · top 20 · Top 500 · long/short](https://data.finzdata.com/s/momentum-63d-skip-21-top-20-top-500-long-short-619b48) | momentum | top500 | 0.45 | 27 | -2.56 |
| [Momentum 126d skip 0 · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-126d-skip-0-top-50-top-1000-long-short-d1ef18) | momentum | top1000 | 0.45 | 33 | 1.15 |
| [Momentum 63d skip 0 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/momentum-63d-skip-0-top-50-top-1000-long-only-d47bf5) | momentum | top1000 | 0.45 | 33 | 1.50 |
| [Trend quality · top 50 · Top 500 · long only](https://data.finzdata.com/s/trend-quality-top-50-top-500-long-only-747951) | expr | top500 | 0.45 | 27 | -1.91 |
| [/GC 50/200 moving-average trend](https://data.finzdata.com/s/gc-50-200-moving-average-trend-78a666) | ma_cross | custom | 0.44 | 8 | 0.00 |
| [Momentum 126d skip 0 · top 20 · Top 1000 · long only](https://data.finzdata.com/s/momentum-126d-skip-0-top-20-top-1000-long-only-3da08a) | momentum | top1000 | 0.43 | 33 | 1.55 |
| [Trend quality · top 20 · Top 1000 · long only](https://data.finzdata.com/s/trend-quality-top-20-top-1000-long-only-666f5c) | expr | top1000 | 0.43 | 33 | -0.39 |
| [AI Trading Championship 5th (11 models): Google Gemma 4 31B value-quality-short momentum](https://data.finzdata.com/s/ai-trading-championship-5th-11-models-google-gemma-4-31b-val-0e4dad) | expr | top1000 | 0.42 | 32 | -0.29 |
| [Sector momentum 12-month · top 3 of 9 sectors (after Moskowitz & Grinblatt 1999)](https://data.finzdata.com/s/sector-momentum-12-month-top-3-of-9-sectors-after-moskowitz-93b4df) | expr | custom | 0.41 | 28 | 0.81 |
| [AI Trading Championship 7th (11 models): Moonshot Kimi K3 quality-value-momentum](https://data.finzdata.com/s/ai-trading-championship-7th-11-models-moonshot-kimi-k3-quali-9a18fb) | expr | top500 | 0.39 | 26 | -2.17 |
| [AI Trading Championship 6th (11 models): Claude Haiku QVM composite](https://data.finzdata.com/s/ai-trading-championship-6th-11-models-claude-haiku-qvm-compo-0f0885) | expr | top500 | 0.39 | 26 | -2.17 |
| [AI Trading Championship 8th (11 models): NVIDIA Nemotron-3 Super value-momentum](https://data.finzdata.com/s/ai-trading-championship-8th-11-models-nvidia-nemotron-3-supe-9c94aa) | expr | top1000 | 0.39 | 32 | 1.76 |
| [AI Trading Championship 9th (11 models): Claude Opus quality-value-momentum-low-vol](https://data.finzdata.com/s/ai-trading-championship-9th-11-models-claude-opus-quality-va-bb573a) | expr | top500 | 0.39 | 26 | -1.27 |
| [FDAX 50/200 moving-average trend](https://data.finzdata.com/s/fdax-50-200-moving-average-trend-b40c18) | ma_cross | custom | 0.37 | 10 | -8.05 |
| [Cross-asset momentum 12-1 · top 4 of 16 ETFs (after Asness, Moskowitz & Pedersen 2013)](https://data.finzdata.com/s/cross-asset-momentum-12-1-top-4-of-16-etfs-after-asness-mosk-039cda) | expr | custom | 0.36 | 28 | -6.17 |
| [Trend quality · top 20 · Top 500 · long only](https://data.finzdata.com/s/trend-quality-top-20-top-500-long-only-a1123d) | expr | top500 | 0.35 | 27 | -0.70 |
| [Momentum 63d skip 0 · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-63d-skip-0-top-50-top-1000-long-short-72c042) | momentum | top1000 | 0.34 | 33 | 1.03 |
| [Momentum 63d skip 21 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/momentum-63d-skip-21-top-50-top-1000-long-only-08c2b3) | momentum | top1000 | 0.31 | 33 | 1.32 |
| [Sector momentum 6-month · top 3 of 9 sectors (after Moskowitz & Grinblatt 1999)](https://data.finzdata.com/s/sector-momentum-6-month-top-3-of-9-sectors-after-moskowitz-g-198f5b) | expr | custom | 0.31 | 28 | -4.31 |
| [Momentum 126d skip 0 · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-126d-skip-0-top-20-top-1000-long-short-e4e466) | momentum | top1000 | 0.31 | 33 | 1.18 |
| [Betting against beta proxy · low 1-year volatility · top 50 · Top 500 · long only (after Frazzini & Pedersen 2014, AQR)](https://data.finzdata.com/s/betting-against-beta-proxy-low-1-year-volatility-top-50-top-2898f2) | expr | top500 | 0.27 | 27 | -1.07 |
| [Volatility-scaled momentum · top 50 · Top 500 (after Barroso & Santa-Clara 2015)](https://data.finzdata.com/s/volatility-scaled-momentum-top-50-top-500-after-barroso-sant-319970) | expr | top500 | 0.27 | 27 | -1.02 |
| [Low volatility · top 50 · Top 500 · long only](https://data.finzdata.com/s/low-volatility-top-50-top-500-long-only-798b06) | expr | top500 | 0.25 | 27 | -1.02 |
| [1-week reversal · top 20 · Top 500 · long only](https://data.finzdata.com/s/1-week-reversal-top-20-top-500-long-only-f2aafd) | expr | top500 | 0.25 | 27 | -5.78 |
| [1-week reversal · top 50 · Top 500 · long only](https://data.finzdata.com/s/1-week-reversal-top-50-top-500-long-only-b67639) | expr | top500 | 0.24 | 27 | -3.91 |
| [Momentum 63d skip 21 · top 50 · Top 500 · long/short](https://data.finzdata.com/s/momentum-63d-skip-21-top-50-top-500-long-short-05b569) | momentum | top500 | 0.24 | 27 | -1.77 |
| [Futures trend · 50/200-day moving average · 26 markets](https://data.finzdata.com/s/futures-trend-50-200-day-moving-average-26-markets-f7a88b) | ma_cross | custom | 0.23 | 8 | 0.00 |
| [Momentum 63d skip 21 · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-63d-skip-21-top-50-top-1000-long-short-aa99d3) | momentum | top1000 | 0.22 | 33 | -1.32 |
| [FDAX 50/200 moving-average trend](https://data.finzdata.com/s/fdax-50-200-moving-average-trend-d10ec7) | ma_cross | custom | 0.21 | 8 | -14.44 |
| [Momentum 63d skip 21 · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-63d-skip-21-top-20-top-1000-long-short-785997) | momentum | top1000 | 0.21 | 33 | -1.07 |
| [Momentum 63d skip 0 · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/momentum-63d-skip-0-top-20-top-1000-long-short-5cd50a) | momentum | top1000 | 0.21 | 33 | 1.35 |
| [52-week-high momentum · top 50 · Top 500 · long only (after George & Hwang 2004)](https://data.finzdata.com/s/52-week-high-momentum-top-50-top-500-long-only-after-george-9fa2ba) | expr | top500 | 0.21 | 27 | -3.29 |
| [Momentum 63d skip 0 · top 20 · Top 1000 · long only](https://data.finzdata.com/s/momentum-63d-skip-0-top-20-top-1000-long-only-5c0737) | momentum | top1000 | 0.20 | 33 | 1.48 |
| [RSI oversold · top 50 · Top 500 · long only](https://data.finzdata.com/s/rsi-oversold-top-50-top-500-long-only-6e3ab0) | expr | top500 | 0.20 | 27 | -1.75 |
| [RSI oversold · top 20 · Top 1000 · long only](https://data.finzdata.com/s/rsi-oversold-top-20-top-1000-long-only-f23fa0) | expr | top1000 | 0.20 | 33 | -0.84 |
| [Trend quality · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/trend-quality-top-20-top-1000-long-short-a4c6de) | expr | top1000 | 0.16 | 33 | -1.29 |
| [RSI oversold · top 50 · Top 1000 · long only](https://data.finzdata.com/s/rsi-oversold-top-50-top-1000-long-only-d64414) | expr | top1000 | 0.16 | 33 | -0.30 |
| [AI Trading Championship 10th (11 models): Claude Sonnet momentum-value-quality long-short](https://data.finzdata.com/s/ai-trading-championship-10th-11-models-claude-sonnet-momentu-4c10ef) | expr | top1000 | 0.15 | 32 | -1.62 |
| [Trend quality · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/trend-quality-top-50-top-1000-long-short-b20d5e) | expr | top1000 | 0.15 | 33 | -1.45 |
| [RSI oversold · top 20 · Top 500 · long only](https://data.finzdata.com/s/rsi-oversold-top-20-top-500-long-only-69903a) | expr | top500 | 0.13 | 27 | -0.39 |
| [Trend quality · top 20 · Top 500 · long/short](https://data.finzdata.com/s/trend-quality-top-20-top-500-long-short-d14106) | expr | top500 | 0.13 | 27 | 4.15 |
| [Commodity momentum 12-1 · top 4 of 13 commodity ETFs (after Miffre & Rallis 2007)](https://data.finzdata.com/s/commodity-momentum-12-1-top-4-of-13-commodity-etfs-after-mif-b37817) | expr | custom | 0.13 | 28 | -3.23 |
| [Momentum 63d skip 21 · top 20 · Top 1000 · long only](https://data.finzdata.com/s/momentum-63d-skip-21-top-20-top-1000-long-only-fdae00) | momentum | top1000 | 0.13 | 33 | 1.51 |
| [Futures cross-sectional momentum 12-1 · long/short · 26 markets (after Asness, Moskowitz & Pedersen 2013)](https://data.finzdata.com/s/futures-cross-sectional-momentum-12-1-long-short-26-markets-1b39bd) | momentum | custom | 0.12 | 8 | -10.00 |
| [1-week reversal · top 50 · Top 1000 · long only](https://data.finzdata.com/s/1-week-reversal-top-50-top-1000-long-only-9cfe26) | expr | top1000 | 0.12 | 33 | 2.07 |
| [Trend quality · top 50 · Top 500 · long/short](https://data.finzdata.com/s/trend-quality-top-50-top-500-long-short-c781ea) | expr | top500 | 0.12 | 27 | 1.98 |
| [/ZN 50/200 moving-average trend](https://data.finzdata.com/s/zn-50-200-moving-average-trend-5b19f2) | ma_cross | custom | 0.11 | 10 | 0.00 |
| [1-week reversal · top 20 · Top 1000 · long only](https://data.finzdata.com/s/1-week-reversal-top-20-top-1000-long-only-925d06) | expr | top1000 | 0.10 | 33 | 1.17 |
| [Commodity futures momentum 12-1 · long/short · top 4 of 17 (after Miffre & Rallis 2007)](https://data.finzdata.com/s/commodity-futures-momentum-12-1-long-short-top-4-of-17-after-c5b2c9) | momentum | custom | 0.09 | 10 | 3.89 |
| [USDJPY trend · 50/200 moving average (spot)](https://data.finzdata.com/s/usdjpy-trend-50-200-moving-average-spot-c6ad3f) | ma_cross | custom | 0.08 | 6 | 0.00 |
| [Low volatility · top 50 · Top 1000 · long only](https://data.finzdata.com/s/low-volatility-top-50-top-1000-long-only-39f53e) | expr | top1000 | 0.08 | 33 | -0.38 |
| [Short-term reversal · sectors · bottom 3 of 9 last month (after Lehmann 1990 and Lo & MacKinlay 1990)](https://data.finzdata.com/s/short-term-reversal-sectors-bottom-3-of-9-last-month-after-l-a21255) | expr | custom | 0.07 | 28 | -1.71 |
| [1-month reversal · top 20 · Top 500 · long only](https://data.finzdata.com/s/1-month-reversal-top-20-top-500-long-only-7d9703) | expr | top500 | 0.07 | 27 | -2.45 |
| [1-week reversal · top 20 · Top 500 · long/short](https://data.finzdata.com/s/1-week-reversal-top-20-top-500-long-short-b2934b) | expr | top500 | 0.07 | 27 | -1.95 |
| [1-month reversal · top 50 · Top 500 · long only](https://data.finzdata.com/s/1-month-reversal-top-50-top-500-long-only-1b0d80) | expr | top500 | 0.06 | 27 | -2.16 |
| [1-month reversal · top 20 · Top 1000 · long only](https://data.finzdata.com/s/1-month-reversal-top-20-top-1000-long-only-967b98) | expr | top1000 | 0.06 | 33 | -0.00 |
| [Low volatility · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/low-volatility-top-50-top-1000-long-short-486a75) | expr | top1000 | 0.05 | 33 | -1.38 |
| [Low volatility · top 20 · Top 500 · long/short](https://data.finzdata.com/s/low-volatility-top-20-top-500-long-short-302b45) | expr | top500 | 0.05 | 27 | 5.94 |
| [/ZN 50/200 moving-average trend](https://data.finzdata.com/s/zn-50-200-moving-average-trend-70909a) | ma_cross | custom | 0.05 | 8 | 0.00 |
| [AI Trading Championship 11th (11 models): Poolside Laguna XS 2.1 earnings yield x 1-day sign](https://data.finzdata.com/s/ai-trading-championship-11th-11-models-poolside-laguna-xs-2-f3727d) | expr | top500 | 0.05 | 26 | -2.89 |
| [1-month reversal · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/1-month-reversal-top-20-top-1000-long-short-8349a3) | expr | top1000 | 0.05 | 33 | -0.99 |
| [Cross-asset momentum 12-1 · long/short 16 ETFs (after Asness, Moskowitz & Pedersen 2013)](https://data.finzdata.com/s/cross-asset-momentum-12-1-long-short-16-etfs-after-asness-mo-0639ad) | expr | custom | 0.04 | 28 | -5.64 |
| [Low volatility · top 20 · Top 500 · long only](https://data.finzdata.com/s/low-volatility-top-20-top-500-long-only-e3fa09) | expr | top500 | 0.04 | 27 | 0.26 |
| [Low volatility · top 50 · Top 500 · long/short](https://data.finzdata.com/s/low-volatility-top-50-top-500-long-short-3d5b9c) | expr | top500 | 0.04 | 27 | 4.71 |
| [1-month reversal · top 50 · Top 1000 · long only](https://data.finzdata.com/s/1-month-reversal-top-50-top-1000-long-only-b12803) | expr | top1000 | 0.04 | 33 | 1.08 |
| [1-week reversal · top 50 · Top 500 · long/short](https://data.finzdata.com/s/1-week-reversal-top-50-top-500-long-short-84b105) | expr | top500 | 0.04 | 27 | 0.05 |
| [RSI oversold · top 20 · Top 500 · long/short](https://data.finzdata.com/s/rsi-oversold-top-20-top-500-long-short-6e74e5) | expr | top500 | 0.04 | 27 | 2.05 |
| [RSI oversold · top 50 · Top 500 · long/short](https://data.finzdata.com/s/rsi-oversold-top-50-top-500-long-short-7da366) | expr | top500 | 0.04 | 27 | 1.21 |
| [/CL 20/100 moving-average trend](https://data.finzdata.com/s/cl-20-100-moving-average-trend-e7bf68) | ma_cross | custom | 0.04 | 10 | -2.62 |
| [RSI oversold · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/rsi-oversold-top-20-top-1000-long-short-4c2efa) | expr | top1000 | 0.03 | 33 | -1.06 |
| [Low volatility · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/low-volatility-top-20-top-1000-long-short-cb099c) | expr | top1000 | 0.03 | 33 | -1.44 |
| [1-week reversal · top 20 · Top 1000 · long/short](https://data.finzdata.com/s/1-week-reversal-top-20-top-1000-long-short-f7516e) | expr | top1000 | 0.03 | 33 | -0.89 |
| [Commodity momentum 12-1 · long/short 13 commodity ETFs (after Miffre & Rallis 2007)](https://data.finzdata.com/s/commodity-momentum-12-1-long-short-13-commodity-etfs-after-m-d58015) | expr | custom | 0.03 | 28 | -0.04 |
| [High-yield currency carry with a trend filter · Mexican peso and Brazilian real futures 50/200](https://data.finzdata.com/s/high-yield-currency-carry-with-a-trend-filter-mexican-peso-a-1dc967) | ma_cross | custom | 0.02 | 7 | -14.52 |
| [1-month reversal · top 20 · Top 500 · long/short](https://data.finzdata.com/s/1-month-reversal-top-20-top-500-long-short-b457eb) | expr | top500 | 0.02 | 27 | -0.31 |
| [1-week reversal · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/1-week-reversal-top-50-top-1000-long-short-3e5a0d) | expr | top1000 | 0.02 | 33 | -0.92 |
| [US Dollar Index trend · /DX 50/200 moving average](https://data.finzdata.com/s/us-dollar-index-trend-dx-50-200-moving-average-7e7c76) | ma_cross | custom | 0.02 | 7 | 9.50 |
| [RSI oversold · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/rsi-oversold-top-50-top-1000-long-short-a63593) | expr | top1000 | 0.01 | 33 | -0.88 |
| [/CL 20/100 moving-average trend](https://data.finzdata.com/s/cl-20-100-moving-average-trend-56ddef) | ma_cross | custom | 0.01 | 8 | -2.59 |
| [52-week-high momentum · top 50 · Top 500 · long/short (after George & Hwang 2004)](https://data.finzdata.com/s/52-week-high-momentum-top-50-top-500-long-short-after-george-e02acd) | expr | top500 | 0.01 | 27 | 2.08 |
| [Commodity futures momentum 12-1 · long/short · top 4 of 17 (after Miffre & Rallis 2007)](https://data.finzdata.com/s/commodity-futures-momentum-12-1-long-short-top-4-of-17-after-3457e6) | momentum | custom | 0.01 | 8 | 3.46 |
| [Currency futures momentum 12-1 · long/short · top 3 of 9 (after Menkhoff, Sarno, Schmeling & Schrimpf 2012)](https://data.finzdata.com/s/currency-futures-momentum-12-1-long-short-top-3-of-9-after-m-a48bfb) | momentum | custom | 0.00 | 7 | -14.38 |
| [1-month reversal · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/1-month-reversal-top-50-top-1000-long-short-d8caf2) | expr | top1000 | 0.00 | 33 | -0.93 |
| [EURUSD trend · 20/100 moving average (spot)](https://data.finzdata.com/s/eurusd-trend-20-100-moving-average-spot-244c6b) | ma_cross | custom | 0.00 | 6 | 0.00 |
| [FX majors trend · 12-month time-series momentum · 7 pairs (spot)](https://data.finzdata.com/s/fx-majors-trend-12-month-time-series-momentum-7-pairs-spot-fc6935) | expr | custom | 0.00 | 6 | -21.41 |
| [Low volatility · top 20 · Top 1000 · long only](https://data.finzdata.com/s/low-volatility-top-20-top-1000-long-only-bac42e) | expr | top1000 | 0.00 | 33 | -0.51 |
| [1-month reversal · top 50 · Top 500 · long/short](https://data.finzdata.com/s/1-month-reversal-top-50-top-500-long-short-63b0d6) | expr | top500 | 0.00 | 27 | -0.33 |
| [Currency futures trend · 12-month time-series momentum · 9 currencies (after Moskowitz, Ooi & Pedersen 2012)](https://data.finzdata.com/s/currency-futures-trend-12-month-time-series-momentum-9-curre-2259e8) | expr | custom | 0.00 | 7 | -15.48 |
| [Betting against beta proxy · low 1-year volatility · top 50 · Top 500 · long/short (after Frazzini & Pedersen 2014, AQR)](https://data.finzdata.com/s/betting-against-beta-proxy-low-1-year-volatility-top-50-top-1bd36c) | expr | top500 | 0.00 | 27 | 4.61 |
| [FX majors rotation · 3-month momentum · top 2 of 7 (spot)](https://data.finzdata.com/s/fx-majors-rotation-3-month-momentum-top-2-of-7-spot-ee1670) | momentum | custom | 0.00 | 6 | 7.43 |
| [20/50 MA Cross Momentum](https://data.finzdata.com/s/20-50-ma-cross-momentum-b6bb11) | ma_cross | top500 | 0.00 | 19 | -2.82 |
| [50/200 MA Cross Trend](https://data.finzdata.com/s/50-200-ma-cross-trend-dcbabb) | ma_cross | top500 | 0.00 | 19 | -2.54 |
| [52-week high anchoring · nearness to high · top 50 · Top 500 · long only](https://data.finzdata.com/s/52-week-high-anchoring-nearness-to-high-top-50-top-500-long-8a412a) | expr | top500 | 0.00 | 19 | -4.96 |
| [52-week high anchoring · top 50 · Top 500 · long only](https://data.finzdata.com/s/52-week-high-anchoring-top-50-top-500-long-only-0c8e81) | expr | top500 | 0.00 | 18 | -5.57 |
| [52-week high proximity · top 50 · Top 1000 · long only](https://data.finzdata.com/s/52-week-high-proximity-top-50-top-1000-long-only-757c7a) | expr | top1000 | 0.00 | 25 | -3.06 |
| [52-week high proximity · top 50 · Top 500 · long only](https://data.finzdata.com/s/52-week-high-proximity-top-50-top-500-long-only-24b3d3) | expr | top500 | 0.00 | 19 | -3.06 |
| [52-week high proximity · top 50 · Top 500 · long only](https://data.finzdata.com/s/52-week-high-proximity-top-50-top-500-long-only-6e5c41) | expr | top500 | 0.00 | 19 | -5.08 |
| [Bitcoin 50/200 moving-average trend](https://data.finzdata.com/s/bitcoin-50-200-moving-average-trend-2a424b) | ma_cross | custom | 0.00 | 38 | -6.89 |
| [Book to Market and Sales Yield Combined Value](https://data.finzdata.com/s/book-to-market-and-sales-yield-combined-value-89731a) | expr | top3000 | 0.00 | 8 | -6.00 |
| [Book-to-Market + Earnings Yield Factor](https://data.finzdata.com/s/book-to-market-earnings-yield-factor-16bb9d) | expr | top500 | 0.00 | 18 | -1.63 |
| [Book to Market Momentum Hybrid Factor](https://data.finzdata.com/s/book-to-market-momentum-hybrid-factor-1e91f2) | expr | top1000 | 0.00 | 16 | -9.50 |
| [Book-to-Market Momentum Top 500 Long Only](https://data.finzdata.com/s/book-to-market-momentum-top-500-long-only-cc9b42) | expr | top500 | 0.00 | 19 | -5.01 |
| [Book To Market Value Factor Top 1000](https://data.finzdata.com/s/book-to-market-value-factor-top-1000-273cc0) | expr | top1000 | 0.00 | 10 | -5.02 |
| [Book to market value with size tilt](https://data.finzdata.com/s/book-to-market-value-with-size-tilt-f71d74) | expr | top1000 | 0.00 | 8 | -10.41 |
| [Cash flow cheapness EBITDA/EV · top 50 · Top 1000 · long only](https://data.finzdata.com/s/cash-flow-cheapness-ebitda-ev-top-50-top-1000-long-only-42c471) | expr | top1000 | 0.00 | 24 | -3.34 |
| [Cash-flow value · top 1000 · monthly](https://data.finzdata.com/s/cash-flow-value-top-1000-monthly-72051a) | expr | top1000 | 0.00 | 25 | 1.66 |
| [Cheap Low Leverage Screen · Top 1000 · Long Only](https://data.finzdata.com/s/cheap-low-leverage-screen-top-1000-long-only-5784e7) | expr | top1000 | 0.00 | 25 | -4.77 |
| [Clipped Momentum Dividend](https://data.finzdata.com/s/clipped-momentum-dividend-6e6885) | expr | top500 | 0.00 | 19 | -1.10 |
| [Composite factor quality value momentum](https://data.finzdata.com/s/composite-factor-quality-value-momentum-b259d3) | expr | top1000 | 0.00 | 8 | -16.45 |
| [Composite Factor · value profitability and low leverage](https://data.finzdata.com/s/composite-factor-value-profitability-and-low-leverage-7a65c2) | expr | top1000 | 0.00 | 3 | -24.22 |
| [Composite Factor Zscore Quality Value](https://data.finzdata.com/s/composite-factor-zscore-quality-value-1f686d) | expr | top1000 | 0.00 | 19 | 2.20 |
| [Composite value quality size · top1000 · long short](https://data.finzdata.com/s/composite-value-quality-size-top1000-long-short-ca2b75) | expr | top1000 | 0.00 | 24 | 5.01 |
| [Crypto cross-sectional momentum · 4-week · top 3 of 15 coins (after Liu, Tsyvinski & Wu 2022)](https://data.finzdata.com/s/crypto-cross-sectional-momentum-4-week-top-3-of-15-coins-aft-7e3ee2) | expr | custom | 0.00 | 38 | -7.49 |
| [Crypto low volatility · 3 lowest-volatility of 15 coins](https://data.finzdata.com/s/crypto-low-volatility-3-lowest-volatility-of-15-coins-2f84da) | expr | custom | 0.00 | 38 | -6.56 |
| [Crypto time-series momentum · 4-week · 15 coins, cash otherwise](https://data.finzdata.com/s/crypto-time-series-momentum-4-week-15-coins-cash-otherwise-5bd4b2) | expr | custom | 0.00 | 38 | -3.35 |
| [Currency momentum · euro, yen, dollar index (after Menkhoff, Sarno, Schmeling & Schrimpf 2012)](https://data.finzdata.com/s/currency-momentum-euro-yen-dollar-index-after-menkhoff-sarno-571084) | expr | custom | 0.00 | 28 | 4.39 |
| [Dist from High Momentum Top 1000 Long Only](https://data.finzdata.com/s/dist-from-high-momentum-top-1000-long-only-327a0d) | expr | top1000 | 0.00 | 25 | 1.72 |
| [Dist High Reversal With Volume Filter](https://data.finzdata.com/s/dist-high-reversal-with-volume-filter-60dc10) | expr | top500 | 0.00 | 14 | -4.22 |
| [Distress-aware deep value composite](https://data.finzdata.com/s/distress-aware-deep-value-composite-fc2ab6) | expr | top3000 | 0.00 | 25 | 1.66 |
| [Dividend low-volatility · top 500 · monthly](https://data.finzdata.com/s/dividend-low-volatility-top-500-monthly-d64d07) | expr | top500 | 0.00 | 19 | -2.64 |
| [Dividend Momentum Blend · Top 500 · Long Only](https://data.finzdata.com/s/dividend-momentum-blend-top-500-long-only-7e80c8) | expr | top500 | 0.00 | 19 | -5.06 |
| [Dividend quality factor with yield and ROE interaction](https://data.finzdata.com/s/dividend-quality-factor-with-yield-and-roe-interaction-b4b756) | expr | top1000 | 0.00 | 4 | 8.99 |
| [Dividend-Sales Yield Factor](https://data.finzdata.com/s/dividend-sales-yield-factor-9098ce) | expr | top500 | 0.00 | 19 | -1.69 |
| [Dividend yield carry · top 50 · Top 500 · long only](https://data.finzdata.com/s/dividend-yield-carry-top-50-top-500-long-only-22bc87) | expr | top500 | 0.00 | 19 | -2.48 |
| [Dividend yield commitment · top 50 · Top 500 · long only](https://data.finzdata.com/s/dividend-yield-commitment-top-50-top-500-long-only-aa794e) | expr | top500 | 0.00 | 18 | -2.10 |
| [Dividend Yield Income Factor Top 500](https://data.finzdata.com/s/dividend-yield-income-factor-top-500-03f42a) | expr | top500 | 0.00 | 9 | -15.49 |
| [Dividend yield mean reversion with volatility filter](https://data.finzdata.com/s/dividend-yield-mean-reversion-with-volatility-filter-7c61a4) | expr | top500 | 0.00 | 3 | -30.54 |
| [Dividend Yield Momentum · 126d momentum × dividend yield · Top 500 · Long Only](https://data.finzdata.com/s/dividend-yield-momentum-126d-momentum-dividend-yield-top-500-b74b61) | expr | top500 | 0.00 | 5 | -20.49 |
| [Dividend Yield Momentum 126d skip 21 · top 50 · Top 500 · long only](https://data.finzdata.com/s/dividend-yield-momentum-126d-skip-21-top-50-top-500-long-onl-504992) | expr | top500 | 0.00 | 16 | -6.80 |
| [Dividend Yield Momentum 12M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/dividend-yield-momentum-12m-skip-1m-top-40-top-1000-long-onl-c3ea05) | expr | top1000 | 0.00 | 24 | 5.32 |
| [Dividend Yield Momentum 12M Skip 1M Top 40 Top1000 Long Only](https://data.finzdata.com/s/dividend-yield-momentum-12m-skip-1m-top-40-top1000-long-only-1057ef) | expr | top1000 | 0.00 | 25 | -3.05 |
| [Dividend Yield Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/dividend-yield-momentum-12m-skip-1m-top-50-top-1000-long-onl-1308f1) | expr | top1000 | 0.00 | 25 | -0.58 |
| [Dividend Yield Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/dividend-yield-momentum-12m-skip-1m-top-50-top-1000-long-onl-88d492) | expr | top1000 | 0.00 | 25 | -0.58 |
| [Dividend Yield Momentum 12M Skip 1M · Top 50 · Top 500 · Long Only](https://data.finzdata.com/s/dividend-yield-momentum-12m-skip-1m-top-50-top-500-long-only-e7e6b8) | expr | top500 | 0.00 | 19 | -6.01 |
| [Dividend Yield Momentum 252d skip 63 top 50 Top 500 long only](https://data.finzdata.com/s/dividend-yield-momentum-252d-skip-63-top-50-top-500-long-onl-3bf0c0) | expr | top500 | 0.00 | 17 | -5.27 |
| [Dividend Yield Momentum 252d Skip 63d · Top 30 · Top 500 · Long Only](https://data.finzdata.com/s/dividend-yield-momentum-252d-skip-63d-top-30-top-500-long-on-59cab2) | expr | top500 | 0.00 | 19 | -6.73 |
| [Dividend Yield Momentum Filter · dividend yield rank times 63d momentum](https://data.finzdata.com/s/dividend-yield-momentum-filter-dividend-yield-rank-times-63d-7f5095) | expr | top1000 | 0.00 | 8 | -11.42 |
| [Dividend Yield Momentum Hybrid](https://data.finzdata.com/s/dividend-yield-momentum-hybrid-c2e1d4) | expr | top500 | 0.00 | 19 | -5.91 |
| [Dividend Yield Momentum Interaction](https://data.finzdata.com/s/dividend-yield-momentum-interaction-1acd31) | expr | top500 | 0.00 | 14 | -5.39 |
| [Dividend Yield Momentum Quality Top 500](https://data.finzdata.com/s/dividend-yield-momentum-quality-top-500-f21c8e) | expr | top500 | 0.00 | 19 | -3.12 |
| [Dividend Yield Momentum Top 1000](https://data.finzdata.com/s/dividend-yield-momentum-top-1000-4e9063) | expr | top1000 | 0.00 | 25 | -3.76 |
| [Dividend Yield Momentum Top 500](https://data.finzdata.com/s/dividend-yield-momentum-top-500-508975) | expr | top500 | 0.00 | 19 | -6.80 |
| [Dividend Yield Momentum Top 500 Long Only](https://data.finzdata.com/s/dividend-yield-momentum-top-500-long-only-690adf) | expr | top500 | 0.00 | 18 | -6.53 |
| [Dividend Yield Momentum Top 500 Long Only](https://data.finzdata.com/s/dividend-yield-momentum-top-500-long-only-6b13a3) | expr | top500 | 0.00 | 18 | -6.26 |
| [Dividend Yield Momentum Top 500 Long Only](https://data.finzdata.com/s/dividend-yield-momentum-top-500-long-only-873b93) | expr | top500 | 0.00 | 19 | -4.39 |
| [Dividend Yield Momentum Top1000 Long Only](https://data.finzdata.com/s/dividend-yield-momentum-top1000-long-only-5a4a55) | expr | top1000 | 0.00 | 25 | -3.51 |
| [Dividend Yield Quality Filtered Factor](https://data.finzdata.com/s/dividend-yield-quality-filtered-factor-68a6c5) | expr | top1000 | 0.00 | 20 | -4.10 |
| [Dividend Yield Quality Screen · yield times ROE](https://data.finzdata.com/s/dividend-yield-quality-screen-yield-times-roe-9c67c8) | expr | top500 | 0.00 | 4 | -38.14 |
| [Dividend yield rank · top 50 · Top 1000 · long only](https://data.finzdata.com/s/dividend-yield-rank-top-50-top-1000-long-only-339034) | expr | top1000 | 0.00 | 25 | -2.42 |
| [Dividend yield · top 50 of the Top 500 · long only](https://data.finzdata.com/s/dividend-yield-top-50-of-the-top-500-long-only-8e4326) | factor | top500 | 0.00 | 27 | 0.15 |
| [Dividend yield · top 50 of the Top 500 · long/short](https://data.finzdata.com/s/dividend-yield-top-50-of-the-top-500-long-short-4347a6) | factor | top500 | 0.00 | 27 | 2.97 |
| [Dividend yield value · top1000 · long only](https://data.finzdata.com/s/dividend-yield-value-top1000-long-only-c29628) | expr | top1000 | 0.00 | 24 | 4.50 |
| [Dividend Yield & Volatility Rank Factor](https://data.finzdata.com/s/dividend-yield-volatility-rank-factor-3a9157) | expr | top500 | 0.00 | 18 | -1.18 |
| [Dual Momentum 12M/1M · Asset Class Proxy · Top 20 · Top 1000 · Long Only](https://data.finzdata.com/s/dual-momentum-12m-1m-asset-class-proxy-top-20-top-1000-long-477495) | expr | top1000 | 0.00 | 25 | -0.83 |
| [Dual Momentum · 12m/1m · Top 500 · Long Only](https://data.finzdata.com/s/dual-momentum-12m-1m-top-500-long-only-308173) | expr | top500 | 0.00 | 0 | 0.00 |
| [Dual Momentum with Skip](https://data.finzdata.com/s/dual-momentum-with-skip-843840) | momentum | top500 | 0.00 | 18 | -5.27 |
| [Earnings-Book Momentum Factor](https://data.finzdata.com/s/earnings-book-momentum-factor-35f0c1) | expr | top500 | 0.00 | 19 | -1.54 |
| [Earnings Momentum · 63d skip 0 · Top 1000 · Long Only](https://data.finzdata.com/s/earnings-momentum-63d-skip-0-top-1000-long-only-1d523c) | expr | top1000 | 0.00 | 0 | 0.00 |
| [Earnings Yield Low Leverage · top 1000 · long only](https://data.finzdata.com/s/earnings-yield-low-leverage-top-1000-long-only-7acca4) | expr | top1000 | 0.00 | 24 | -1.90 |
| [Earnings Yield Mean Reversion](https://data.finzdata.com/s/earnings-yield-mean-reversion-5c6aad) | expr | top500 | 0.00 | 18 | -3.00 |
| [Earnings Yield Mean Reversion Top 500](https://data.finzdata.com/s/earnings-yield-mean-reversion-top-500-3d5214) | expr | top500 | 0.00 | 19 | -2.84 |
| [Earnings Yield Minus Leverage Factor](https://data.finzdata.com/s/earnings-yield-minus-leverage-factor-6e7c48) | expr | top1000 | 0.00 | 25 | -3.10 |
| [Earnings Yield Momentum 126d Skip 21 Top 30 Top500 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-126d-skip-21-top-30-top500-long-only-a129d9) | expr | top500 | 0.00 | 17 | -6.99 |
| [Earnings Yield Momentum · 126d skip 21 · Top 500 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-126d-skip-21-top-500-long-only-352657) | expr | top500 | 0.00 | 0 | 0.00 |
| [Earnings Yield Momentum 126d Skip 21d · Top 30 · Top 500 · Long/Short](https://data.finzdata.com/s/earnings-yield-momentum-126d-skip-21d-top-30-top-500-long-sh-002db6) | expr | top500 | 0.00 | 19 | 1.51 |
| [Earnings Yield Momentum 126d · top 50 · Top 1000 · long only](https://data.finzdata.com/s/earnings-yield-momentum-126d-top-50-top-1000-long-only-ddae4b) | expr | top1000 | 0.00 | 26 | 3.49 |
| [Earnings Yield Momentum 12M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-12m-skip-1m-top-40-top-1000-long-onl-50db01) | expr | top1000 | 0.00 | 31 | -1.04 |
| [Earnings Yield Momentum 12M Skip 1M · Top 40 · Top 500 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-12m-skip-1m-top-40-top-500-long-only-b438d3) | expr | top500 | 0.00 | 19 | -6.03 |
| [Earnings Yield Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-12m-skip-1m-top-50-top-1000-long-onl-09b19b) | expr | top1000 | 0.00 | 22 | -7.12 |
| [Earnings Yield Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-12m-skip-1m-top-50-top-1000-long-onl-5d8de3) | expr | top1000 | 0.00 | 23 | -7.43 |
| [Earnings Yield Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-12m-skip-1m-top-50-top-1000-long-onl-bd1676) | expr | top1000 | 0.00 | 20 | -10.27 |
| [Earnings Yield Momentum 12M Skip 1M · Top 50 · Top 500 · Long/Short](https://data.finzdata.com/s/earnings-yield-momentum-12m-skip-1m-top-50-top-500-long-shor-5d1d5c) | expr | top500 | 0.00 | 19 | 2.50 |
| [Earnings Yield Momentum 12M \| Top 500 \| Long Only](https://data.finzdata.com/s/earnings-yield-momentum-12m-top-500-long-only-09f44d) | expr | top500 | 0.00 | 14 | -5.91 |
| [Earnings Yield Momentum 252d skip 63 · top 40 · Top 500 · long only](https://data.finzdata.com/s/earnings-yield-momentum-252d-skip-63-top-40-top-500-long-onl-c0565e) | expr | top500 | 0.00 | 16 | -6.21 |
| [Earnings Yield Momentum 252d skip 63 top 50 Top 1000 long only](https://data.finzdata.com/s/earnings-yield-momentum-252d-skip-63-top-50-top-1000-long-on-0f6919) | expr | top1000 | 0.00 | 23 | -1.74 |
| [Earnings Yield Momentum 6M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-6m-skip-1m-top-40-top-1000-long-only-eb710d) | expr | top1000 | 0.00 | 25 | -1.19 |
| [Earnings Yield Momentum](https://data.finzdata.com/s/earnings-yield-momentum-927666) | expr | top500 | 0.00 | 18 | -6.17 |
| [Earnings Yield Momentum 9M Skip 1M · Top 50 · Top 500 · Long Only](https://data.finzdata.com/s/earnings-yield-momentum-9m-skip-1m-top-50-top-500-long-only-135654) | expr | top500 | 0.00 | 19 | -5.57 |
| [Earnings Yield Momentum 9M Skip 1M Top 50 Top1000 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-9m-skip-1m-top-50-top1000-long-only-7a7c1e) | expr | top1000 | 0.00 | 25 | -1.20 |
| [Earnings Yield Momentum Long/Short Top 500](https://data.finzdata.com/s/earnings-yield-momentum-long-short-top-500-ec7cca) | expr | top500 | 0.00 | 19 | 0.48 |
| [Earnings Yield Momentum Top 1000 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-top-1000-long-only-0ec714) | expr | top1000 | 0.00 | 22 | -7.12 |
| [Earnings Yield Momentum Top 1000 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-top-1000-long-only-2a5c67) | expr | top1000 | 0.00 | 24 | 0.81 |
| [Earnings Yield Momentum Top 1000 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-top-1000-long-only-7c8e72) | expr | top1000 | 0.00 | 25 | 0.51 |
| [Earnings Yield Momentum Top 1000 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-top-1000-long-only-93e6a8) | expr | top1000 | 0.00 | 23 | -7.43 |
| [Earnings Yield Momentum Top 1000 Monthly](https://data.finzdata.com/s/earnings-yield-momentum-top-1000-monthly-0cec24) | expr | top1000 | 0.00 | 25 | 1.12 |
| [Earnings Yield Momentum Top 500 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-top-500-long-only-a75bf7) | expr | top500 | 0.00 | 19 | -5.57 |
| [Earnings Yield Momentum Top 500 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-top-500-long-only-fd0676) | expr | top500 | 0.00 | 18 | -5.76 |
| [Earnings Yield Momentum Top1000 Long Only](https://data.finzdata.com/s/earnings-yield-momentum-top1000-long-only-982b2e) | expr | top1000 | 0.00 | 23 | -7.43 |
| [Earnings Yield Momentum with Volatility Scaling](https://data.finzdata.com/s/earnings-yield-momentum-with-volatility-scaling-fd8f08) | expr | top1000 | 0.00 | 6 | -24.07 |
| [Earnings Yield Rank Long Only Top 500](https://data.finzdata.com/s/earnings-yield-rank-long-only-top-500-86c611) | expr | top500 | 0.00 | 9 | -15.07 |
| [Earnings yield rank · top 50 · Top 500 · long only](https://data.finzdata.com/s/earnings-yield-rank-top-50-top-500-long-only-7be7e1) | expr | top500 | 0.00 | 19 | -2.97 |
| [Earnings Yield Size Adjusted Factor](https://data.finzdata.com/s/earnings-yield-size-adjusted-factor-008c35) | expr | top3000 | 0.00 | 20 | -3.41 |
| [Earnings-yield small-cap tilt · top 3000 · monthly](https://data.finzdata.com/s/earnings-yield-small-cap-tilt-top-3000-monthly-03320b) | expr | top3000 | 0.00 | 25 | 3.16 |
| [Earnings Yield Trend · 50/200 MA on earnings yield · Top 500 · Long Only](https://data.finzdata.com/s/earnings-yield-trend-50-200-ma-on-earnings-yield-top-500-lon-6049e0) | ma_cross | top500 | 0.00 | 5 | -8.23 |
| [Earnings yield value · top 20 · Top 500 · long only](https://data.finzdata.com/s/earnings-yield-value-top-20-top-500-long-only-5cca16) | expr | top500 | 0.00 | 19 | -3.86 |
| [EBITDA EV Composite Value Top 1000](https://data.finzdata.com/s/ebitda-ev-composite-value-top-1000-147c61) | expr | top1000 | 0.00 | 10 | 9.16 |
| [EBITDA/EV Momentum 12M Skip 1M · Top 30 · Top 1000 · Long/Short](https://data.finzdata.com/s/ebitda-ev-momentum-12m-skip-1m-top-30-top-1000-long-short-0c4c0a) | expr | top1000 | 0.00 | 25 | -1.96 |
| [EBITDA/EV Momentum 12M Skip 1M Top 30 Top500 Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-12m-skip-1m-top-30-top500-long-only-a4ed5c) | expr | top500 | 0.00 | 19 | -4.76 |
| [EBITDA/EV Momentum 12M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-12m-skip-1m-top-40-top-1000-long-only-03223b) | expr | top1000 | 0.00 | 25 | 0.48 |
| [EBITDA/EV Momentum 12M Skip 1M · Top 40 · Top 500 · Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-12m-skip-1m-top-40-top-500-long-only-f23111) | expr | top500 | 0.00 | 19 | -4.96 |
| [EBITDA/EV Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-12m-skip-1m-top-50-top-1000-long-only-c25be5) | expr | top1000 | 0.00 | 25 | 0.68 |
| [EBITDA/EV Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-12m-skip-1m-top-50-top-1000-long-only-ef47fe) | expr | top1000 | 0.00 | 24 | 1.45 |
| [EBITDA/EV Momentum 12M \| Top 500 \| Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-12m-top-500-long-only-856d12) | expr | top500 | 0.00 | 14 | -4.90 |
| [EBITDA/EV Momentum 252d skip 0 top 30 Top 1000 long only](https://data.finzdata.com/s/ebitda-ev-momentum-252d-skip-0-top-30-top-1000-long-only-1b79e2) | expr | top1000 | 0.00 | 23 | 0.36 |
| [EBITDA/EV Momentum 252d skip 63 · top 40 · Top 1000 · long only](https://data.finzdata.com/s/ebitda-ev-momentum-252d-skip-63-top-40-top-1000-long-only-4e45a4) | expr | top1000 | 0.00 | 22 | -3.27 |
| [EBITDA/EV Momentum · 252d skip 63 · Top 500 · Long-Short](https://data.finzdata.com/s/ebitda-ev-momentum-252d-skip-63-top-500-long-short-b7f00f) | expr | top500 | 0.00 | 5 | 11.05 |
| [EBITDA/EV Momentum 63d · top 30 · Top 500 · long only](https://data.finzdata.com/s/ebitda-ev-momentum-63d-top-30-top-500-long-only-c4315c) | expr | top500 | 0.00 | 20 | -7.15 |
| [EBITDA/EV Momentum Composite Top 500](https://data.finzdata.com/s/ebitda-ev-momentum-composite-top-500-b4ad54) | expr | top500 | 0.00 | 19 | -5.19 |
| [EBITDA/EV + Momentum Factor](https://data.finzdata.com/s/ebitda-ev-momentum-factor-baaabb) | expr | top500 | 0.00 | 18 | -5.27 |
| [EBITDA/EV Momentum Top 1000](https://data.finzdata.com/s/ebitda-ev-momentum-top-1000-50e29e) | expr | top1000 | 0.00 | 25 | -1.16 |
| [EBITDA/EV Momentum Top 1000 Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-top-1000-long-only-f1ae69) | expr | top1000 | 0.00 | 25 | 0.68 |
| [EBITDA/EV Momentum Top 500](https://data.finzdata.com/s/ebitda-ev-momentum-top-500-2c26e7) | expr | top500 | 0.00 | 19 | -4.83 |
| [EBITDA/EV Momentum Top 500 Long Only](https://data.finzdata.com/s/ebitda-ev-momentum-top-500-long-only-cbdba7) | expr | top500 | 0.00 | 19 | -4.02 |
| [EBITDA/EV Momentum Top 500 Long/Short](https://data.finzdata.com/s/ebitda-ev-momentum-top-500-long-short-052353) | expr | top500 | 0.00 | 18 | 5.16 |
| [EBITDA EV Quality Factor with Size Tilt](https://data.finzdata.com/s/ebitda-ev-quality-factor-with-size-tilt-14dd0b) | expr | top1000 | 0.00 | 6 | -28.19 |
| [EBITDA EV quality value long short](https://data.finzdata.com/s/ebitda-ev-quality-value-long-short-d87f2e) | expr | top1000 | 0.00 | 8 | 9.38 |
| [EBITDA EV Sales Yield Quality Factor](https://data.finzdata.com/s/ebitda-ev-sales-yield-quality-factor-23d682) | expr | top500 | 0.00 | 14 | -3.32 |
| [EBITDA/EV Value 12M Momentum · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/ebitda-ev-value-12m-momentum-top-40-top-1000-long-only-930827) | expr | top1000 | 0.00 | 25 | 0.48 |
| [EBITDA/EV Value 252d Momentum Confirmation · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/ebitda-ev-value-252d-momentum-confirmation-top-40-top-1000-l-bf3e0d) | expr | top1000 | 0.00 | 25 | -3.23 |
| [EBITDA/EV value momentum hybrid](https://data.finzdata.com/s/ebitda-ev-value-momentum-hybrid-f550eb) | expr | top1000 | 0.00 | 3 | -42.82 |
| [EBITDA/EV Value Top 1000 Quarterly](https://data.finzdata.com/s/ebitda-ev-value-top-1000-quarterly-b0cd9b) | expr | top1000 | 0.00 | 25 | -3.65 |
| [EBITDA EV Value with Size Tilt](https://data.finzdata.com/s/ebitda-ev-value-with-size-tilt-89d2f4) | expr | top1000 | 0.00 | 25 | -4.60 |
| [EBITDA over EV rank · top 50 · Top 1000 · long only](https://data.finzdata.com/s/ebitda-over-ev-rank-top-50-top-1000-long-only-a30299) | expr | top1000 | 0.00 | 25 | -3.65 |
| [EBITDA-RoE Value Factor](https://data.finzdata.com/s/ebitda-roe-value-factor-7cf199) | expr | top500 | 0.00 | 19 | -1.54 |
| [EEM 20/100 moving-average trend](https://data.finzdata.com/s/eem-20-100-moving-average-trend-5000af) | ma_cross | custom | 0.00 | 28 | -3.16 |
| [EEM 50/200 moving-average trend](https://data.finzdata.com/s/eem-50-200-moving-average-trend-94bd5e) | ma_cross | custom | 0.00 | 28 | -4.87 |
| [EFA 20/100 moving-average trend](https://data.finzdata.com/s/efa-20-100-moving-average-trend-b1d363) | ma_cross | custom | 0.00 | 28 | 0.00 |
| [EFA 50/200 moving-average trend](https://data.finzdata.com/s/efa-50-200-moving-average-trend-c52fff) | ma_cross | custom | 0.00 | 28 | -2.93 |
| [Enterprise value yield · top500 · long only](https://data.finzdata.com/s/enterprise-value-yield-top500-long-only-fe3d27) | expr | top500 | 0.00 | 18 | -0.73 |
| [Ethereum 50/200 moving-average trend](https://data.finzdata.com/s/ethereum-50-200-moving-average-trend-86a176) | ma_cross | custom | 0.00 | 38 | -5.82 |
| [EV value EBITDA/EV · top 50 · Top 1000 · long only](https://data.finzdata.com/s/ev-value-ebitda-ev-top-50-top-1000-long-only-a01eff) | expr | top1000 | 0.00 | 25 | -3.50 |
| [expr book to market momentum reversal long short](https://data.finzdata.com/s/expr-book-to-market-momentum-reversal-long-short-59e27b) | expr | top1000 | 0.00 | 25 | 4.31 |
| [Expr Dividend Yield Mean Reversion Top 3000](https://data.finzdata.com/s/expr-dividend-yield-mean-reversion-top-3000-b664bb) | expr | top3000 | 0.00 | 16 | -1.50 |
| [Expr dividend yield reversal with volatility filter](https://data.finzdata.com/s/expr-dividend-yield-reversal-with-volatility-filter-3a0c89) | expr | top1000 | 0.00 | 10 | 6.75 |
| [expr dividend yield vol adjusted factor long only](https://data.finzdata.com/s/expr-dividend-yield-vol-adjusted-factor-long-only-639142) | expr | top1000 | 0.00 | 25 | -2.50 |
| [expr earnings yield size adjusted quality long only](https://data.finzdata.com/s/expr-earnings-yield-size-adjusted-quality-long-only-d16f8f) | expr | top1000 | 0.00 | 25 | -3.28 |
| [Expr earnings yield zscore demeaned momentum](https://data.finzdata.com/s/expr-earnings-yield-zscore-demeaned-momentum-63f4bf) | expr | top500 | 0.00 | 9 | -14.38 |
| [Expr Low Vol Mean Reversion RSI](https://data.finzdata.com/s/expr-low-vol-mean-reversion-rsi-798575) | expr | top500 | 0.00 | 19 | -0.69 |
| [Expr Momentum Reversal Skip Fundamentals](https://data.finzdata.com/s/expr-momentum-reversal-skip-fundamentals-a37bdf) | expr | top1000 | 0.00 | 29 | 0.60 |
| [expr quality earnings yield vol filter](https://data.finzdata.com/s/expr-quality-earnings-yield-vol-filter-2ff34c) | expr | top1000 | 0.00 | 24 | -2.12 |
| [Expr Quality Momentum Rank](https://data.finzdata.com/s/expr-quality-momentum-rank-53021f) | expr | top1000 | 0.00 | 25 | 0.05 |
| [Expr Quality Momentum Skip Factor Top 500](https://data.finzdata.com/s/expr-quality-momentum-skip-factor-top-500-92b6f5) | expr | top500 | 0.00 | 12 | -0.15 |
| [Expr Quality Value Hybrid EBITDA](https://data.finzdata.com/s/expr-quality-value-hybrid-ebitda-84469c) | expr | top1000 | 0.00 | 25 | -5.61 |
| [Expr RSI Mean Reversion Top 1000](https://data.finzdata.com/s/expr-rsi-mean-reversion-top-1000-8de7be) | expr | top1000 | 0.00 | 14 | 0.02 |
| [expr rsi mean reversion vol filter long short](https://data.finzdata.com/s/expr-rsi-mean-reversion-vol-filter-long-short-93978f) | expr | top500 | 0.00 | 19 | 4.74 |
| [Expr Value Composite Earnings Yield](https://data.finzdata.com/s/expr-value-composite-earnings-yield-a4cf75) | expr | top1000 | 0.00 | 25 | -3.90 |
| [expr value momentum hybrid rank](https://data.finzdata.com/s/expr-value-momentum-hybrid-rank-d4a2a4) | expr | top1000 | 0.00 | 24 | 4.34 |
| [Expr Value Momentum Hybrid Top 1000](https://data.finzdata.com/s/expr-value-momentum-hybrid-top-1000-40f857) | expr | top1000 | 0.00 | 14 | -11.03 |
| [expr value momentum quality composite long only](https://data.finzdata.com/s/expr-value-momentum-quality-composite-long-only-a0450f) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Expr value momentum quality composite long only](https://data.finzdata.com/s/expr-value-momentum-quality-composite-long-only-d3c427) | expr | top1000 | 0.00 | 10 | -11.29 |
| [Expr Value Quality Composite Rank](https://data.finzdata.com/s/expr-value-quality-composite-rank-daea2b) | expr | top1000 | 0.00 | 29 | 0.47 |
| [Expr Volatility Adjusted Momentum Top 500](https://data.finzdata.com/s/expr-volatility-adjusted-momentum-top-500-708af0) | expr | top500 | 0.00 | 12 | -4.38 |
| [Factor Book-to-Market + 12-1 Momentum (Monthly, Top 1000, L/S)](https://data.finzdata.com/s/factor-book-to-market-12-1-momentum-monthly-top-1000-l-s-da886e) | expr | top1000 | 0.00 | 25 | -1.33 |
| [Factor Book-to-Market - Log Market Cap (Monthly, Top 3000, L/S)](https://data.finzdata.com/s/factor-book-to-market-log-market-cap-monthly-top-3000-l-s-750c72) | expr | top3000 | 0.00 | 25 | 6.37 |
| [Factor book to market sales yield long short](https://data.finzdata.com/s/factor-book-to-market-sales-yield-long-short-9ed466) | expr | top1000 | 0.00 | 10 | 3.61 |
| [Factor Book To Market Size Adjusted](https://data.finzdata.com/s/factor-book-to-market-size-adjusted-4a5470) | expr | top3000 | 0.00 | 29 | -0.68 |
| [Factor Book to Market Size Adjusted Top 1000](https://data.finzdata.com/s/factor-book-to-market-size-adjusted-top-1000-e50151) | expr | top1000 | 0.00 | 14 | 1.01 |
| [Factor composite quality value momentum](https://data.finzdata.com/s/factor-composite-quality-value-momentum-a82067) | expr | top1000 | 0.00 | 4 | -36.99 |
| [Factor: Dividend Yield and Momentum](https://data.finzdata.com/s/factor-dividend-yield-and-momentum-953589) | expr | top500 | 0.00 | 19 | -7.32 |
| [Factor Dividend Yield + Earnings Yield (Quarterly, Top 1000, L/S)](https://data.finzdata.com/s/factor-dividend-yield-earnings-yield-quarterly-top-1000-l-s-ded7ad) | expr | top1000 | 0.00 | 25 | 1.38 |
| [Factor Earnings Yield - 252d Volatility (Monthly, Top 1000, L/S)](https://data.finzdata.com/s/factor-earnings-yield-252d-volatility-monthly-top-1000-l-s-f7920e) | expr | top1000 | 0.00 | 25 | -2.23 |
| [Factor Earnings Yield + 6-1 Momentum (Monthly, Top 500, L/S)](https://data.finzdata.com/s/factor-earnings-yield-6-1-momentum-monthly-top-500-l-s-28f2ab) | expr | top500 | 0.00 | 19 | 1.84 |
| [Factor: Earnings Yield Momentum](https://data.finzdata.com/s/factor-earnings-yield-momentum-b565eb) | expr | top500 | 0.00 | 19 | -7.45 |
| [Factor Earnings Yield + ROE - Leverage (Monthly, Top 1000, L/S)](https://data.finzdata.com/s/factor-earnings-yield-roe-leverage-monthly-top-1000-l-s-310740) | expr | top1000 | 0.00 | 25 | -0.47 |
| [Factor: EBITDA/EV and Momentum](https://data.finzdata.com/s/factor-ebitda-ev-and-momentum-f5c656) | expr | top500 | 0.00 | 19 | -4.53 |
| [Factor EBITDA EV + ROE (Monthly, Top 1000, L/S)](https://data.finzdata.com/s/factor-ebitda-ev-roe-monthly-top-1000-l-s-f9f693) | expr | top1000 | 0.00 | 25 | 1.29 |
| [Factor ebitda ev roe quality spread](https://data.finzdata.com/s/factor-ebitda-ev-roe-quality-spread-9081a4) | expr | top1000 | 0.00 | 10 | 6.88 |
| [Factor: Log Market Cap and Momentum](https://data.finzdata.com/s/factor-log-market-cap-and-momentum-df6ec8) | expr | top500 | 0.00 | 19 | -3.55 |
| [Factor momentum · 252d momentum of earnings yield](https://data.finzdata.com/s/factor-momentum-252d-momentum-of-earnings-yield-9b5540) | expr | top1000 | 0.00 | 3 | -30.05 |
| [Factor Momentum 252d Skip 63 Long Short](https://data.finzdata.com/s/factor-momentum-252d-skip-63-long-short-bea594) | momentum | top1000 | 0.00 | 6 | 6.20 |
| [Factor Quality Composite Rank Top 100](https://data.finzdata.com/s/factor-quality-composite-rank-top-100-77342b) | expr | top1000 | 0.00 | 20 | -5.17 |
| [Factor: ROE vs Leverage](https://data.finzdata.com/s/factor-roe-vs-leverage-3747a9) | expr | top500 | 0.00 | 19 | -3.38 |
| [Factor Sales Yield - Leverage (Quarterly, Top 3000, L/S)](https://data.finzdata.com/s/factor-sales-yield-leverage-quarterly-top-3000-l-s-26d1f3) | expr | top3000 | 0.00 | 25 | -1.76 |
| [Factor Value Quality Composite · Monthly · Top 1000](https://data.finzdata.com/s/factor-value-quality-composite-monthly-top-1000-d6f6b5) | expr | top1000 | 0.00 | 25 | 1.53 |
| [Fast trend 20-100 · top 1000 · daily](https://data.finzdata.com/s/fast-trend-20-100-top-1000-daily-ea561f) | ma_cross | top1000 | 0.00 | 25 | 0.86 |
| [Fundamental‑Adjusted MA Cross](https://data.finzdata.com/s/fundamental-adjusted-ma-cross-3b6379) | ma_cross | top500 | 0.00 | 18 | -3.99 |
| [Fundamental‑Adjusted Momentum with Cost](https://data.finzdata.com/s/fundamental-adjusted-momentum-with-cost-86f0a2) | expr | top500 | 0.00 | 18 | -4.10 |
| [GLD 20/100 moving-average trend](https://data.finzdata.com/s/gld-20-100-moving-average-trend-cf1e8e) | ma_cross | custom | 0.00 | 28 | 0.00 |
| [GLD 50/200 moving-average trend](https://data.finzdata.com/s/gld-50-200-moving-average-trend-3fa4e0) | ma_cross | custom | 0.00 | 28 | 0.00 |
| [Golden cross 50-200 · top 500 · weekly](https://data.finzdata.com/s/golden-cross-50-200-top-500-weekly-2ccb06) | ma_cross | top500 | 0.00 | 19 | -2.94 |
| [Growth quality composite factor](https://data.finzdata.com/s/growth-quality-composite-factor-954d5f) | expr | top1000 | 0.00 | 8 | -1.44 |
| [High‑Return Low‑Vol MA Cross](https://data.finzdata.com/s/high-return-low-vol-ma-cross-28771e) | ma_cross | top500 | 0.00 | 18 | -5.75 |
| [Industry-Neutral Momentum · 126d skip 21 · Top 1000 · Long-Short](https://data.finzdata.com/s/industry-neutral-momentum-126d-skip-21-top-1000-long-short-8f4700) | expr | top1000 | 0.00 | 0 | 0.00 |
| [Intermediate 189-day momentum with one-week skip](https://data.finzdata.com/s/intermediate-189-day-momentum-with-one-week-skip-4419c5) | momentum | top3000 | 0.00 | 25 | -5.48 |
| [Intermediate Momentum Skip 63 Top 500 Long Only](https://data.finzdata.com/s/intermediate-momentum-skip-63-top-500-long-only-817bdb) | momentum | top500 | 0.00 | 19 | -3.59 |
| [Intermediate Trend Crossover 20/100 · Top 1000 · Long/Short](https://data.finzdata.com/s/intermediate-trend-crossover-20-100-top-1000-long-short-25675f) | ma_cross | top1000 | 0.00 | 25 | 1.47 |
| [IWM 20/100 moving-average trend](https://data.finzdata.com/s/iwm-20-100-moving-average-trend-8492aa) | ma_cross | custom | 0.00 | 28 | 0.00 |
| [IWM 50/200 moving-average trend](https://data.finzdata.com/s/iwm-50-200-moving-average-trend-ad6dbe) | ma_cross | custom | 0.00 | 28 | -1.93 |
| [Leverage Adjusted Momentum 126d skip 21 top 40 Top 1000 long_short](https://data.finzdata.com/s/leverage-adjusted-momentum-126d-skip-21-top-40-top-1000-long-f92f5d) | expr | top1000 | 0.00 | 23 | -3.16 |
| [Leverage-Adjusted Momentum 126d · top 50 · Top 1000 · long only](https://data.finzdata.com/s/leverage-adjusted-momentum-126d-top-50-top-1000-long-only-f047c4) | expr | top1000 | 0.00 | 26 | 0.14 |
| [Leverage-Adjusted Momentum 12M Skip 1M Low Leverage Top 50 Top500 Long Only](https://data.finzdata.com/s/leverage-adjusted-momentum-12m-skip-1m-low-leverage-top-50-t-59044f) | expr | top500 | 0.00 | 19 | -3.92 |
| [Leverage-Adjusted Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/leverage-adjusted-momentum-12m-skip-1m-top-50-top-1000-long-0b9e2b) | expr | top1000 | 0.00 | 22 | -0.42 |
| [Leverage-Adjusted Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/leverage-adjusted-momentum-12m-skip-1m-top-50-top-1000-long-2a90a3) | expr | top1000 | 0.00 | 25 | -2.85 |
| [Leverage-Adjusted Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/leverage-adjusted-momentum-12m-skip-1m-top-50-top-1000-long-5d0a25) | expr | top1000 | 0.00 | 24 | -4.19 |
| [Leverage-Adjusted Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/leverage-adjusted-momentum-12m-skip-1m-top-50-top-1000-long-f3c1b2) | expr | top1000 | 0.00 | 25 | -2.58 |
| [Leverage-Adjusted Momentum 12M Skip 1M · Top 50 · Top 500 · Long Only](https://data.finzdata.com/s/leverage-adjusted-momentum-12m-skip-1m-top-50-top-500-long-o-71455d) | expr | top500 | 0.00 | 19 | -4.02 |
| [Leverage-Adjusted Momentum 252d skip 0 · top 30 · Top 500 · long/short](https://data.finzdata.com/s/leverage-adjusted-momentum-252d-skip-0-top-30-top-500-long-s-4b549b) | expr | top500 | 0.00 | 16 | -5.65 |
| [Leverage-Adjusted Momentum 6M Skip 1M · Top 30 · Top 500 · Long/Short](https://data.finzdata.com/s/leverage-adjusted-momentum-6m-skip-1m-top-30-top-500-long-sh-1896d9) | expr | top500 | 0.00 | 18 | -7.43 |
| [Leverage-Adjusted Momentum Top 1000 Long Only](https://data.finzdata.com/s/leverage-adjusted-momentum-top-1000-long-only-e9bc71) | expr | top1000 | 0.00 | 24 | -4.30 |
| [Leverage-Adjusted Momentum Top500 Long Short](https://data.finzdata.com/s/leverage-adjusted-momentum-top500-long-short-128712) | expr | top500 | 0.00 | 19 | -4.14 |
| [Leverage-adjusted value · top 1000 · quarterly](https://data.finzdata.com/s/leverage-adjusted-value-top-1000-quarterly-b18fef) | expr | top1000 | 0.00 | 25 | -2.93 |
| [Leverage Risk Factor Long Short Top 1000](https://data.finzdata.com/s/leverage-risk-factor-long-short-top-1000-8fa6f0) | expr | top1000 | 0.00 | 10 | -5.66 |
| [Long-horizon 50/200 regime crossover](https://data.finzdata.com/s/long-horizon-50-200-regime-crossover-be6846) | ma_cross | top3000 | 0.00 | 25 | -2.50 |
| [Low Leverage High Dividend Yield Factor](https://data.finzdata.com/s/low-leverage-high-dividend-yield-factor-391f14) | expr | top1000 | 0.00 | 6 | -20.22 |
| [Low Leverage Momentum Long/Short Top 1000](https://data.finzdata.com/s/low-leverage-momentum-long-short-top-1000-96e887) | expr | top1000 | 0.00 | 25 | -3.37 |
| [Low leverage momentum quality · book to market and 126d momentum](https://data.finzdata.com/s/low-leverage-momentum-quality-book-to-market-and-126d-moment-5e7092) | expr | top1000 | 0.00 | 3 | -35.10 |
| [Low Leverage Momentum Top 1000](https://data.finzdata.com/s/low-leverage-momentum-top-1000-a6c762) | expr | top1000 | 0.00 | 25 | -0.50 |
| [Low Leverage Momentum Top 1000 Long Only](https://data.finzdata.com/s/low-leverage-momentum-top-1000-long-only-00b6bc) | expr | top1000 | 0.00 | 24 | -4.28 |
| [Low Leverage Momentum Top 3000](https://data.finzdata.com/s/low-leverage-momentum-top-3000-7a7285) | expr | top3000 | 0.00 | 24 | -4.57 |
| [Low Leverage Momentum Top 500](https://data.finzdata.com/s/low-leverage-momentum-top-500-286635) | expr | top500 | 0.00 | 19 | -5.86 |
| [Low Leverage Momentum Top 500 Long Only](https://data.finzdata.com/s/low-leverage-momentum-top-500-long-only-4c48b2) | expr | top500 | 0.00 | 17 | -5.82 |
| [Low Leverage Quality · book to market minus leverage](https://data.finzdata.com/s/low-leverage-quality-book-to-market-minus-leverage-7b63fe) | expr | top1000 | 0.00 | 4 | -20.72 |
| [Low Leverage Quality · negative leverage rank with size control](https://data.finzdata.com/s/low-leverage-quality-negative-leverage-rank-with-size-contro-84964c) | expr | top1000 | 0.00 | 8 | -14.96 |
| [Low vol 126d · top 50 · Top 500 · long only](https://data.finzdata.com/s/low-vol-126d-top-50-top-500-long-only-cb1182) | expr | top500 | 0.00 | 19 | -1.89 |
| [Low vol 63d · top 50 · Top 1000 · long only](https://data.finzdata.com/s/low-vol-63d-top-50-top-1000-long-only-89f1bf) | expr | top1000 | 0.00 | 25 | -1.66 |
| [Low Vol Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/low-vol-momentum-12m-skip-1m-top-50-top-1000-long-only-884e86) | expr | top1000 | 0.00 | 25 | -0.11 |
| [Low Vol Momentum 12M Skip 1M Vol Target Top 40 Top500 Long Only](https://data.finzdata.com/s/low-vol-momentum-12m-skip-1m-vol-target-top-40-top500-long-o-07ae9b) | expr | top500 | 0.00 | 19 | -3.39 |
| [Low Vol Momentum 12M \| Top 500 \| Long Only](https://data.finzdata.com/s/low-vol-momentum-12m-top-500-long-only-1230e2) | expr | top500 | 0.00 | 14 | -4.02 |
| [Low Vol Momentum 252d · top 40 · Top 500 · long only](https://data.finzdata.com/s/low-vol-momentum-252d-top-40-top-500-long-only-0f3038) | expr | top500 | 0.00 | 20 | -3.12 |
| [Low Vol Momentum 63d skip 0 top 40 Top 500 long only](https://data.finzdata.com/s/low-vol-momentum-63d-skip-0-top-40-top-500-long-only-5c7581) | expr | top500 | 0.00 | 17 | -2.66 |
| [Low Vol Momentum 63d Skip 21 Top 50 Top500 Long Only](https://data.finzdata.com/s/low-vol-momentum-63d-skip-21-top-50-top500-long-only-065c61) | expr | top500 | 0.00 | 17 | -2.90 |
| [Low Vol Momentum 6M Skip 1M Top 40 Top500 Long Only](https://data.finzdata.com/s/low-vol-momentum-6m-skip-1m-top-40-top500-long-only-e5835b) | expr | top500 | 0.00 | 20 | -2.00 |
| [Low Vol Momentum Top 500 Long Only](https://data.finzdata.com/s/low-vol-momentum-top-500-long-only-6b44f9) | expr | top500 | 0.00 | 19 | -3.18 |
| [Low Vol Momentum Top 500 Long Only](https://data.finzdata.com/s/low-vol-momentum-top-500-long-only-ae0401) | expr | top500 | 0.00 | 19 | -3.27 |
| [Low Vol Momentum Top500 Long Only](https://data.finzdata.com/s/low-vol-momentum-top500-long-only-2050a2) | expr | top500 | 0.00 | 19 | -3.57 |
| [Low Vol Momentum Top500 Long Only](https://data.finzdata.com/s/low-vol-momentum-top500-long-only-51a3e1) | expr | top500 | 0.00 | 20 | -1.93 |
| [Low Vol Value Screen · Top 500 · Long Only](https://data.finzdata.com/s/low-vol-value-screen-top-500-long-only-71cac8) | expr | top500 | 0.00 | 19 | -2.13 |
| [Low volatility · 126d · top 50 · Top 1000 · long only](https://data.finzdata.com/s/low-volatility-126d-top-50-top-1000-long-only-fad49b) | expr | top1000 | 0.00 | 25 | -2.46 |
| [Low volatility 63d · top 50 · Top 3000 · long only](https://data.finzdata.com/s/low-volatility-63d-top-50-top-3000-long-only-da44ea) | expr | top3000 | 0.00 | 25 | -5.55 |
| [Low Volatility Anomaly Top 1000 Long Only](https://data.finzdata.com/s/low-volatility-anomaly-top-1000-long-only-7f99e3) | expr | top1000 | 0.00 | 24 | -0.72 |
| [Low volatility anomaly · top1000 · long only](https://data.finzdata.com/s/low-volatility-anomaly-top1000-long-only-c5c1e8) | expr | top1000 | 0.00 | 24 | -1.72 |
| [Low Volatility Dividend Yield Factor](https://data.finzdata.com/s/low-volatility-dividend-yield-factor-0d0a48) | expr | top1000 | 0.00 | 16 | -5.18 |
| [Low volatility dividend yield factor](https://data.finzdata.com/s/low-volatility-dividend-yield-factor-fe87ed) | expr | top500 | 0.00 | 8 | -13.72 |
| [Low Volatility Minimum Variance Rank](https://data.finzdata.com/s/low-volatility-minimum-variance-rank-8e5b3b) | expr | top1000 | 0.00 | 20 | 0.00 |
| [Low Volatility Momentum 126d skip 21 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/low-volatility-momentum-126d-skip-21-top-50-top-1000-long-on-f24643) | expr | top1000 | 0.00 | 22 | -4.66 |
| [Low-Volatility Momentum · 126d skip 21 · Top 500 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-126d-skip-21-top-500-long-only-4c24c2) | expr | top500 | 0.00 | 0 | 0.00 |
| [Low Volatility Momentum 12M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-12m-skip-1m-top-40-top-1000-long-onl-5702ac) | expr | top1000 | 0.00 | 25 | -0.43 |
| [Low Volatility Momentum 12M Skip 1M · Vol Filter · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-12m-skip-1m-vol-filter-top-50-top-10-49319f) | expr | top1000 | 0.00 | 25 | -3.51 |
| [Low Volatility Momentum 252d Skip 63d · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-252d-skip-63d-top-50-top-1000-long-o-34c261) | expr | top1000 | 0.00 | 25 | -0.88 |
| [Low-Volatility Momentum · 63d skip 0 · Top 500 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-63d-skip-0-top-500-long-only-77dd84) | expr | top500 | 0.00 | 0 | 0.00 |
| [Low Volatility Momentum 6M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-6m-skip-1m-top-40-top-1000-long-only-2f6b7c) | expr | top1000 | 0.00 | 25 | -2.45 |
| [Low Volatility Momentum 6M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-6m-skip-1m-top-40-top-1000-long-only-70b6ed) | expr | top1000 | 0.00 | 22 | -5.16 |
| [Low Volatility Momentum 6M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-6m-skip-1m-top-40-top-1000-long-only-99e1d4) | expr | top1000 | 0.00 | 24 | -2.78 |
| [Low Volatility Momentum 6M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/low-volatility-momentum-6m-skip-1m-top-50-top-1000-long-only-d640c1) | expr | top1000 | 0.00 | 25 | -2.71 |
| [Low Volatility Momentum Top 500 Long Only](https://data.finzdata.com/s/low-volatility-momentum-top-500-long-only-5add8a) | expr | top500 | 0.00 | 18 | -5.04 |
| [Low Volatility Momentum Top 500 Long Only](https://data.finzdata.com/s/low-volatility-momentum-top-500-long-only-6ebe8b) | expr | top500 | 0.00 | 18 | -2.67 |
| [Low Volatility Momentum Top 500 Long Only](https://data.finzdata.com/s/low-volatility-momentum-top-500-long-only-828adf) | expr | top500 | 0.00 | 18 | -3.89 |
| [Low Volatility Momentum Top 500 Long Only](https://data.finzdata.com/s/low-volatility-momentum-top-500-long-only-87b551) | expr | top500 | 0.00 | 17 | -4.18 |
| [Low Volatility Momentum Top 500 Long Only](https://data.finzdata.com/s/low-volatility-momentum-top-500-long-only-ac06f2) | expr | top500 | 0.00 | 19 | -4.49 |
| [Low Volatility Momentum Top 500 Long Only](https://data.finzdata.com/s/low-volatility-momentum-top-500-long-only-c60d18) | expr | top500 | 0.00 | 19 | -3.14 |
| [Low volatility quality via inverse realized vol and book to market](https://data.finzdata.com/s/low-volatility-quality-via-inverse-realized-vol-and-book-to-e45dfc) | expr | top1000 | 0.00 | 4 | -6.27 |
| [Low Volatility RSI Mean Reversion](https://data.finzdata.com/s/low-volatility-rsi-mean-reversion-cbfdf4) | expr | top1000 | 0.00 | 19 | 0.00 |
| [Low-volatility short-term reversal](https://data.finzdata.com/s/low-volatility-short-term-reversal-224e35) | expr | top1000 | 0.00 | 25 | -0.92 |
| [MA cross 10/200 regime · top 20 · Top 500 · long only](https://data.finzdata.com/s/ma-cross-10-200-regime-top-20-top-500-long-only-58d8e5) | ma_cross | top500 | 0.00 | 18 | -5.44 |
| [MA Cross 10 30 Momentum Filtered](https://data.finzdata.com/s/ma-cross-10-30-momentum-filtered-e60b07) | ma_cross | top500 | 0.00 | 14 | -6.87 |
| [MA Cross: 10/50 for Top 3000](https://data.finzdata.com/s/ma-cross-10-50-for-top-3000-26aa0f) | ma_cross | top3000 | 0.00 | 25 | 3.17 |
| [MA Cross 10/50 · Russell 2000 small cap trend](https://data.finzdata.com/s/ma-cross-10-50-russell-2000-small-cap-trend-155186) | ma_cross | top1000 | 0.00 | 8 | -13.23 |
| [MA cross 10-50 · top3000 · long short](https://data.finzdata.com/s/ma-cross-10-50-top3000-long-short-276f33) | ma_cross | top3000 | 0.00 | 24 | 0.80 |
| [MA Cross 10-60 (Weekly, Top 1000, Long)](https://data.finzdata.com/s/ma-cross-10-60-weekly-top-1000-long-4eb9da) | ma_cross | top1000 | 0.00 | 25 | 2.64 |
| [MA cross 20/100 on Russell 2000 proxy](https://data.finzdata.com/s/ma-cross-20-100-on-russell-2000-proxy-4626fb) | ma_cross | custom | 0.00 | 9 | 0.00 |
| [MA cross 20/100 · top 100 · Top 1000 · long only](https://data.finzdata.com/s/ma-cross-20-100-top-100-top-1000-long-only-1dd1f1) | ma_cross | top1000 | 0.00 | 25 | 1.48 |
| [MA cross 20/100 · top 50 · Top 500 · long only](https://data.finzdata.com/s/ma-cross-20-100-top-50-top-500-long-only-5be994) | ma_cross | top500 | 0.00 | 19 | -1.36 |
| [MA Cross 20/100 Top 500 Long Only](https://data.finzdata.com/s/ma-cross-20-100-top-500-long-only-62c45b) | ma_cross | top500 | 0.00 | 19 | -1.20 |
| [MA cross 20-100 · top1000 · long only](https://data.finzdata.com/s/ma-cross-20-100-top1000-long-only-831be4) | ma_cross | top1000 | 0.00 | 24 | 0.95 |
| [MA Cross 20/100 · top3000 US equity trend](https://data.finzdata.com/s/ma-cross-20-100-top3000-us-equity-trend-d08c26) | ma_cross | top3000 | 0.00 | 10 | -4.55 |
| [MA cross · 20/100 volume weighted close trend](https://data.finzdata.com/s/ma-cross-20-100-volume-weighted-close-trend-fe91cc) | ma_cross | top500 | 0.00 | 3 | 5.76 |
| [MA Cross 20/100 Weekly Top 1000 Long Only](https://data.finzdata.com/s/ma-cross-20-100-weekly-top-1000-long-only-b6ccb2) | ma_cross | top1000 | 0.00 | 25 | 1.41 |
| [MA Cross: 20/200 for Top 500](https://data.finzdata.com/s/ma-cross-20-200-for-top-500-26d34a) | ma_cross | top500 | 0.00 | 19 | -2.92 |
| [MA Cross 20/200 on Top 500](https://data.finzdata.com/s/ma-cross-20-200-on-top-500-2e8b7b) | ma_cross | top500 | 0.00 | 18 | -4.08 |
| [MA Cross 20 50 Long Short Top 1000](https://data.finzdata.com/s/ma-cross-20-50-long-short-top-1000-858b1a) | ma_cross | top1000 | 0.00 | 20 | 0.00 |
| [MA cross 20 50 mean reversion short](https://data.finzdata.com/s/ma-cross-20-50-mean-reversion-short-13ed31) | ma_cross | top500 | 0.00 | 9 | 4.95 |
| [MA Cross 20 50 Momentum Filter](https://data.finzdata.com/s/ma-cross-20-50-momentum-filter-4df655) | ma_cross | top1000 | 0.00 | 16 | -8.44 |
| [MA Cross 20/50 · Top 1000 · long only](https://data.finzdata.com/s/ma-cross-20-50-top-1000-long-only-18f823) | ma_cross | top1000 | 0.00 | 22 | -1.48 |
| [MA Cross 20/50 Top 1000 Long Only](https://data.finzdata.com/s/ma-cross-20-50-top-1000-long-only-1bf297) | ma_cross | top1000 | 0.00 | 24 | -3.16 |
| [MA Cross 20/50 · Top 1000 · long only](https://data.finzdata.com/s/ma-cross-20-50-top-1000-long-only-5ef9a0) | ma_cross | top1000 | 0.00 | 26 | 2.33 |
| [MA Cross 20/50 Top 1000 Long Only](https://data.finzdata.com/s/ma-cross-20-50-top-1000-long-only-dbcead) | ma_cross | top1000 | 0.00 | 25 | 2.10 |
| [MA Cross 20 50 Top 1000 long_short](https://data.finzdata.com/s/ma-cross-20-50-top-1000-long-short-0f39b3) | ma_cross | top1000 | 0.00 | 23 | 2.18 |
| [MA Cross 20 50 Top 1000 Long Short](https://data.finzdata.com/s/ma-cross-20-50-top-1000-long-short-e2d138) | ma_cross | top1000 | 0.00 | 25 | 2.84 |
| [MA Cross 20 50 Top 3000 Long Short](https://data.finzdata.com/s/ma-cross-20-50-top-3000-long-short-88f8d3) | ma_cross | top3000 | 0.00 | 12 | 0.00 |
| [MA cross 30/150 on top 500 equal weight](https://data.finzdata.com/s/ma-cross-30-150-on-top-500-equal-weight-c7f6ec) | ma_cross | top500 | 0.00 | 4 | 0.70 |
| [MA Cross 40-160 (Monthly, Top 500, Long/Short)](https://data.finzdata.com/s/ma-cross-40-160-monthly-top-500-long-short-2debfa) | ma_cross | top500 | 0.00 | 19 | 0.61 |
| [MA cross 5/150 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/ma-cross-5-150-top-50-top-1000-long-only-59a31a) | ma_cross | top1000 | 0.00 | 25 | 1.38 |
| [MA Cross 50-200 Day Top 1000 Long Only](https://data.finzdata.com/s/ma-cross-50-200-day-top-1000-long-only-62b07a) | ma_cross | top1000 | 0.00 | 24 | -2.77 |
| [MA Cross 50 200 Factor Tilt](https://data.finzdata.com/s/ma-cross-50-200-factor-tilt-8fd3ad) | ma_cross | top1000 | 0.00 | 19 | 0.00 |
| [MA Cross: 50/200 for Top 1000](https://data.finzdata.com/s/ma-cross-50-200-for-top-1000-9bb0f1) | ma_cross | top1000 | 0.00 | 25 | 1.66 |
| [MA Cross 50 200 Long Only Top 500](https://data.finzdata.com/s/ma-cross-50-200-long-only-top-500-f21090) | ma_cross | top500 | 0.00 | 14 | -2.19 |
| [MA Cross 50 200 Top 1000 Long Only](https://data.finzdata.com/s/ma-cross-50-200-top-1000-long-only-47076e) | ma_cross | top1000 | 0.00 | 10 | -9.66 |
| [MA Cross 50/200 Top 1000 Long Only](https://data.finzdata.com/s/ma-cross-50-200-top-1000-long-only-a1562b) | ma_cross | top1000 | 0.00 | 25 | 1.26 |
| [MA Cross 50 200 Top 3000 Long Only](https://data.finzdata.com/s/ma-cross-50-200-top-3000-long-only-908123) | ma_cross | top3000 | 0.00 | 18 | -5.22 |
| [MA cross 50/200 · top 50 · Top 1000 · long only](https://data.finzdata.com/s/ma-cross-50-200-top-50-top-1000-long-only-fa94e5) | ma_cross | top1000 | 0.00 | 25 | 1.67 |
| [MA cross 50/200 · top 50 · Top 500 · long only](https://data.finzdata.com/s/ma-cross-50-200-top-50-top-500-long-only-830759) | ma_cross | top500 | 0.00 | 19 | -2.88 |
| [MA cross 50/200 · top 50 · Top 500 · long only · weekly](https://data.finzdata.com/s/ma-cross-50-200-top-50-top-500-long-only-weekly-40e6e1) | ma_cross | top500 | 0.00 | 19 | -2.25 |
| [MA Cross 50/200 Top 500 Long Only](https://data.finzdata.com/s/ma-cross-50-200-top-500-long-only-349ca6) | ma_cross | top500 | 0.00 | 18 | -2.15 |
| [MA Cross 50/200 Top 500 Long Only](https://data.finzdata.com/s/ma-cross-50-200-top-500-long-only-4374a2) | ma_cross | top500 | 0.00 | 19 | -2.83 |
| [MA Cross 50 200 Top 500 Long Only](https://data.finzdata.com/s/ma-cross-50-200-top-500-long-only-71cfef) | ma_cross | top500 | 0.00 | 19 | -2.88 |
| [MA Cross 50 200 Top 500 long only](https://data.finzdata.com/s/ma-cross-50-200-top-500-long-only-d1c786) | ma_cross | top500 | 0.00 | 17 | -4.15 |
| [MA Cross 50/200 · Top 500 · long only](https://data.finzdata.com/s/ma-cross-50-200-top-500-long-only-dcb5f1) | ma_cross | top500 | 0.00 | 20 | -2.68 |
| [MA Cross 50-200 Top1000 Long Only](https://data.finzdata.com/s/ma-cross-50-200-top1000-long-only-6aded9) | ma_cross | top1000 | 0.00 | 25 | 0.68 |
| [MA Cross 50/200 Trend Filter · Top 100 · Top 1000 · Long Only](https://data.finzdata.com/s/ma-cross-50-200-trend-filter-top-100-top-1000-long-only-89e1b4) | ma_cross | top1000 | 0.00 | 25 | 0.63 |
| [MA Cross 50/200 Trend Filter · Top 500 · Long Only](https://data.finzdata.com/s/ma-cross-50-200-trend-filter-top-500-long-only-c1f3c4) | ma_cross | top500 | 0.00 | 19 | -2.83 |
| [MA Cross 50/200 Trend Filter · Top 500 · Long Only](https://data.finzdata.com/s/ma-cross-50-200-trend-filter-top-500-long-only-d85fd4) | ma_cross | top500 | 0.00 | 25 | -1.23 |
| [MA Cross 50/200 Trend Filter · Top 500 · Long Only](https://data.finzdata.com/s/ma-cross-50-200-trend-filter-top-500-long-only-f068e1) | ma_cross | top500 | 0.00 | 16 | -4.47 |
| [MA cross 50 200 with volume confirmation](https://data.finzdata.com/s/ma-cross-50-200-with-volume-confirmation-ffd78e) | ma_cross | top1000 | 0.00 | 10 | -9.56 |
| [MA Cross Fast 20 Slow 100 Long Short](https://data.finzdata.com/s/ma-cross-fast-20-slow-100-long-short-80d54c) | ma_cross | top500 | 0.00 | 19 | 4.45 |
| [ma_cross fast 20 slow 100 top3000 long only](https://data.finzdata.com/s/ma-cross-fast-20-slow-100-top3000-long-only-3d7d40) | ma_cross | top3000 | 0.00 | 25 | -0.81 |
| [MA Cross Fast 21 Slow 63 Top 1000 Long Short](https://data.finzdata.com/s/ma-cross-fast-21-slow-63-top-1000-long-short-2ddf6b) | ma_cross | top1000 | 0.00 | 14 | 2.74 |
| [MA Cross Fast 50 Slow 200 Long Only](https://data.finzdata.com/s/ma-cross-fast-50-slow-200-long-only-2e1220) | ma_cross | top1000 | 0.00 | 25 | 0.72 |
| [MA Cross Fast 50 Slow 200 Top 500 Long Only](https://data.finzdata.com/s/ma-cross-fast-50-slow-200-top-500-long-only-ad13e2) | ma_cross | top500 | 0.00 | 12 | -3.15 |
| [ma_cross fast 50 slow 200 top500 long only](https://data.finzdata.com/s/ma-cross-fast-50-slow-200-top500-long-only-7ef054) | ma_cross | top500 | 0.00 | 19 | -2.69 |
| [MA Cross Small Cap · 50/200 on top 3000](https://data.finzdata.com/s/ma-cross-small-cap-50-200-on-top-3000-cdbf5d) | ma_cross | top3000 | 0.00 | 3 | -44.41 |
| [MA Cross Trend Filter Momentum Top 500](https://data.finzdata.com/s/ma-cross-trend-filter-momentum-top-500-f1ff89) | expr | top500 | 0.00 | 19 | -2.92 |
| [MA Cross Trend Follower Top 500](https://data.finzdata.com/s/ma-cross-trend-follower-top-500-99ed4d) | ma_cross | top500 | 0.00 | 19 | -2.69 |
| [MA Cross with Adaptive Fast Period](https://data.finzdata.com/s/ma-cross-with-adaptive-fast-period-a924fa) | ma_cross | top500 | 0.00 | 18 | -2.75 |
| [MA Cross with Volatility Filter](https://data.finzdata.com/s/ma-cross-with-volatility-filter-d942b6) | ma_cross | top500 | 0.00 | 18 | -5.94 |
| [Magic formula · EBITDA/EV plus ROE · top 50 of the Top 500 (after Greenblatt 2005) · long only](https://data.finzdata.com/s/magic-formula-ebitda-ev-plus-roe-top-50-of-the-top-500-after-3a6bc8) | expr | top500 | 0.00 | 27 | -0.06 |
| [Magic formula · EBITDA/EV plus ROE · top 50 of the Top 500 (after Greenblatt 2005) · long/short](https://data.finzdata.com/s/magic-formula-ebitda-ev-plus-roe-top-50-of-the-top-500-after-d3d059) | expr | top500 | 0.00 | 27 | 4.32 |
| [Managed-futures trend · 1, 3 and 12-month blend · 23 futures ETFs (after Hurst, Ooi & Pedersen 2017, AQR)](https://data.finzdata.com/s/managed-futures-trend-1-3-and-12-month-blend-23-futures-etfs-1a2e56) | expr | custom | 0.00 | 28 | -7.59 |
| [Managed-futures trend · 12-month · 23 futures ETFs (after Hurst, Ooi & Pedersen 2017, AQR)](https://data.finzdata.com/s/managed-futures-trend-12-month-23-futures-etfs-after-hurst-o-53620d) | expr | custom | 0.00 | 28 | -9.18 |
| [Medium-term 20/100 trend crossover](https://data.finzdata.com/s/medium-term-20-100-trend-crossover-9c935b) | ma_cross | top3000 | 0.00 | 25 | 0.11 |
| [Mid Cap 50/200 MA Cross Long Only](https://data.finzdata.com/s/mid-cap-50-200-ma-cross-long-only-1e6d60) | ma_cross | top1000 | 0.00 | 6 | -15.59 |
| [Momentum 126d Skip 63 Top 1000 Long Short](https://data.finzdata.com/s/momentum-126d-skip-63-top-1000-long-short-ba33dd) | momentum | top1000 | 0.00 | 19 | -4.02 |
| [Momentum 189d Skip 21 Top 100 Long Only](https://data.finzdata.com/s/momentum-189d-skip-21-top-100-long-only-46859a) | momentum | top1000 | 0.00 | 6 | -13.33 |
| [Momentum 189d Skip 21 Top 500 Long Only](https://data.finzdata.com/s/momentum-189d-skip-21-top-500-long-only-c45842) | momentum | top500 | 0.00 | 19 | -4.30 |
| [Momentum 252d skip 63 top 100 long only](https://data.finzdata.com/s/momentum-252d-skip-63-top-100-long-only-357ca4) | momentum | top1000 | 0.00 | 8 | -11.35 |
| [Momentum 252d skip 63 top 1000 long only](https://data.finzdata.com/s/momentum-252d-skip-63-top-1000-long-only-76e058) | momentum | top1000 | 0.00 | 10 | -9.11 |
| [Momentum 252d Skip 63 Top 3000 Long Only](https://data.finzdata.com/s/momentum-252d-skip-63-top-3000-long-only-244df5) | momentum | top3000 | 0.00 | 18 | -5.11 |
| [Momentum 252d skip 63 · top1000 long short](https://data.finzdata.com/s/momentum-252d-skip-63-top1000-long-short-74f07c) | momentum | top1000 | 0.00 | 8 | -2.41 |
| [Momentum 42d Skip 21 Top 100 Long Only](https://data.finzdata.com/s/momentum-42d-skip-21-top-100-long-only-bdace0) | momentum | top1000 | 0.00 | 25 | -2.31 |
| [Momentum 63d Skip 21 Top 1000 Long/Short](https://data.finzdata.com/s/momentum-63d-skip-21-top-1000-long-short-e4f937) | momentum | top1000 | 0.00 | 25 | -3.46 |
| [Momentum 63d skip 21 top 30 top500 long only](https://data.finzdata.com/s/momentum-63d-skip-21-top-30-top500-long-only-b2c8c2) | momentum | top500 | 0.00 | 4 | -7.43 |
| [Momentum Skip Factor · 252d momentum with 63d skip](https://data.finzdata.com/s/momentum-skip-factor-252d-momentum-with-63d-skip-4cf0bb) | momentum | top1000 | 0.00 | 3 | -7.83 |
| [Multi-asset trend following · 6 and 12 month · 16 ETFs (after Hamill, Rattray & Van Hemert 2016, Man Group)](https://data.finzdata.com/s/multi-asset-trend-following-6-and-12-month-16-etfs-after-ham-6c2015) | expr | custom | 0.00 | 28 | -5.28 |
| [Multi-factor defensive · value, quality, momentum, low volatility · top 50 of the Top 500 (after Man Group and AQR style premia) · long/short](https://data.finzdata.com/s/multi-factor-defensive-value-quality-momentum-low-volatility-570a20) | expr | top500 | 0.00 | 27 | 2.74 |
| [Multi-factor defensive · value, quality, momentum, low volatility · top 50 of the Top 500 (after Man Group and AQR style premia) · long only](https://data.finzdata.com/s/multi-factor-defensive-value-quality-momentum-low-volatility-d26442) | expr | top500 | 0.00 | 27 | -1.03 |
| [Multi-Factor Momentum 12M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/multi-factor-momentum-12m-skip-1m-top-40-top-1000-long-only-8ce2de) | expr | top1000 | 0.00 | 24 | -3.04 |
| [Multi-Factor Momentum 12M Skip 1M · Top 40 · Top 1000 · Long/Short](https://data.finzdata.com/s/multi-factor-momentum-12m-skip-1m-top-40-top-1000-long-short-ab00a6) | expr | top1000 | 0.00 | 24 | 0.74 |
| [Multi-Factor Momentum Top 500 Long Only](https://data.finzdata.com/s/multi-factor-momentum-top-500-long-only-2fc10f) | expr | top500 | 0.00 | 18 | -3.54 |
| [Neglected small caps · top 100 · Top 3000 · long only](https://data.finzdata.com/s/neglected-small-caps-top-100-top-3000-long-only-12735d) | expr | top3000 | 0.00 | 24 | -4.85 |
| [One month reversal · top 50 · Top 1000 · long only](https://data.finzdata.com/s/one-month-reversal-top-50-top-1000-long-only-e75164) | expr | top1000 | 0.00 | 24 | -0.79 |
| [Post-news 10/40 trend crossover](https://data.finzdata.com/s/post-news-10-40-trend-crossover-17aeb5) | ma_cross | top1000 | 0.00 | 25 | 3.32 |
| [Profitability quality · top500 · long only](https://data.finzdata.com/s/profitability-quality-top500-long-only-9c4bfa) | expr | top500 | 0.00 | 18 | -4.18 |
| [Profitability Screen · ROE and EBITDA/EV combined rank](https://data.finzdata.com/s/profitability-screen-roe-and-ebitda-ev-combined-rank-ccac3b) | expr | top1000 | 0.00 | 8 | -5.99 |
| [Profitability screen using EBITDA/EV and sales yield](https://data.finzdata.com/s/profitability-screen-using-ebitda-ev-and-sales-yield-c1226d) | expr | top1000 | 0.00 | 4 | -6.90 |
| [QQQ 20/100 moving-average trend](https://data.finzdata.com/s/qqq-20-100-moving-average-trend-1de48a) | ma_cross | custom | 0.00 | 28 | -3.48 |
| [QQQ 50/200 moving-average trend](https://data.finzdata.com/s/qqq-50-200-moving-average-trend-d8ad99) | ma_cross | custom | 0.00 | 28 | -4.47 |
| [Quality · high ROE, low leverage · top 50 of the Top 500 (after Asness, Frazzini & Pedersen 2019, AQR) · long only](https://data.finzdata.com/s/quality-high-roe-low-leverage-top-50-of-the-top-500-after-as-9f2796) | expr | top500 | 0.00 | 27 | -3.79 |
| [Quality · high ROE, low leverage · top 50 of the Top 500 (after Asness, Frazzini & Pedersen 2019, AQR) · long/short](https://data.finzdata.com/s/quality-high-roe-low-leverage-top-50-of-the-top-500-after-as-e57455) | expr | top500 | 0.00 | 27 | -3.13 |
| [Quality minus junk · high ROE, low leverage · top 50 · Top 500 · long/short](https://data.finzdata.com/s/quality-minus-junk-high-roe-low-leverage-top-50-top-500-long-e33ad7) | expr | top500 | 0.00 | 19 | 0.25 |
| [Quality Minus Junk ROE Leverage Screen](https://data.finzdata.com/s/quality-minus-junk-roe-leverage-screen-08af40) | expr | top1000 | 0.00 | 19 | -2.71 |
| [Quality minus junk · ROE minus leverage long short](https://data.finzdata.com/s/quality-minus-junk-roe-minus-leverage-long-short-bfcf0d) | expr | top1000 | 0.00 | 3 | -8.15 |
| [Quality minus junk via ROE and leverage screen](https://data.finzdata.com/s/quality-minus-junk-via-roe-and-leverage-screen-0a50d2) | expr | top1000 | 0.00 | 4 | -3.10 |
| [Quality minus leverage long only](https://data.finzdata.com/s/quality-minus-leverage-long-only-5e2aaa) | expr | top1000 | 0.00 | 8 | -13.94 |
| [Quality Momentum 126d skip 21 top 30 Top 1000 long only](https://data.finzdata.com/s/quality-momentum-126d-skip-21-top-30-top-1000-long-only-d0d7ba) | expr | top1000 | 0.00 | 23 | -3.81 |
| [Quality Momentum · 126d skip 21 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-126d-skip-21-top-500-long-only-1d0cc4) | expr | top500 | 0.00 | 0 | 0.00 |
| [Quality Momentum · 126d skip 21 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-126d-skip-21-top-500-long-only-532990) | expr | top500 | 0.00 | 0 | 0.00 |
| [Quality-Momentum 126d Skip 21d · Top 40 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-126d-skip-21d-top-40-top-500-long-only-d53ed0) | expr | top500 | 0.00 | 19 | -4.45 |
| [Quality-Momentum 12M Skip 1M · ROE Filter · Top 30 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-12m-skip-1m-roe-filter-top-30-top-500-long-fb2be6) | expr | top500 | 0.00 | 19 | -3.69 |
| [Quality Momentum 12M Skip 1M ROE Top 30 Top500 Long Only](https://data.finzdata.com/s/quality-momentum-12m-skip-1m-roe-top-30-top500-long-only-e7f984) | expr | top500 | 0.00 | 20 | -4.15 |
| [Quality-Momentum 12M Skip 1M · Top 30 · Top 1000 · Long Only](https://data.finzdata.com/s/quality-momentum-12m-skip-1m-top-30-top-1000-long-only-3aeca0) | expr | top1000 | 0.00 | 31 | 0.69 |
| [Quality-Momentum 12M Skip 1M · Top 30 · Top 1000 · Long Only](https://data.finzdata.com/s/quality-momentum-12m-skip-1m-top-30-top-1000-long-only-ebc238) | expr | top1000 | 0.00 | 25 | -2.57 |
| [Quality-Momentum 12M Skip 1M · Top 30 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-12m-skip-1m-top-30-top-500-long-only-de0f22) | expr | top500 | 0.00 | 18 | -5.53 |
| [Quality Momentum 252d Skip 63 Top 30 Top500 Long Only](https://data.finzdata.com/s/quality-momentum-252d-skip-63-top-30-top500-long-only-d387a0) | expr | top500 | 0.00 | 17 | -5.85 |
| [Quality-Momentum 63d skip 0 · top 30 · Top 1000 · long only](https://data.finzdata.com/s/quality-momentum-63d-skip-0-top-30-top-1000-long-only-d366f1) | expr | top1000 | 0.00 | 22 | -4.38 |
| [Quality-Momentum 63d skip 21 · top 30 · Top 1000 · long only](https://data.finzdata.com/s/quality-momentum-63d-skip-21-top-30-top-1000-long-only-07f10e) | expr | top1000 | 0.00 | 26 | -3.24 |
| [Quality-Momentum 6M Skip 1M ROE Top 30 Top1000 Long Only](https://data.finzdata.com/s/quality-momentum-6m-skip-1m-roe-top-30-top1000-long-only-839c1b) | expr | top1000 | 0.00 | 25 | -3.12 |
| [Quality-Momentum 6M Skip 1M · Top 30 · Top 500 · Long/Short](https://data.finzdata.com/s/quality-momentum-6m-skip-1m-top-30-top-500-long-short-fbdf39) | expr | top500 | 0.00 | 19 | 1.24 |
| [Quality Momentum Composite · 126d momentum × ROE · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-composite-126d-momentum-roe-top-500-long-on-09a1b8) | expr | top500 | 0.00 | 5 | -30.43 |
| [Quality-Momentum Composite Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-composite-top-1000-long-only-9ba9cb) | expr | top1000 | 0.00 | 23 | -0.69 |
| [Quality-Momentum Factor Top 1000](https://data.finzdata.com/s/quality-momentum-factor-top-1000-948ee1) | expr | top1000 | 0.00 | 25 | -3.97 |
| [Quality Momentum Low Vol Top 1000](https://data.finzdata.com/s/quality-momentum-low-vol-top-1000-24622e) | expr | top1000 | 0.00 | 25 | -0.48 |
| [Quality Momentum ROE 12M Skip 1M \| Top 1000 \| Long Only](https://data.finzdata.com/s/quality-momentum-roe-12m-skip-1m-top-1000-long-only-a1d53f) | expr | top1000 | 0.00 | 20 | -4.37 |
| [Quality Momentum ROE 12M Skip 1M · Top 30 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-roe-12m-skip-1m-top-30-top-500-long-only-6bbf1d) | expr | top500 | 0.00 | 19 | -4.99 |
| [Quality Momentum ROE Filter 12M Skip 1M · Top 30 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-roe-filter-12m-skip-1m-top-30-top-500-long-9f202f) | expr | top500 | 0.00 | 19 | -3.69 |
| [Quality Momentum ROE Filter 12M Skip 1M · Top 30 · Top 500 · Long Only](https://data.finzdata.com/s/quality-momentum-roe-filter-12m-skip-1m-top-30-top-500-long-fdd5e4) | expr | top500 | 0.00 | 16 | -5.93 |
| [Quality Momentum Top 1000](https://data.finzdata.com/s/quality-momentum-top-1000-b5676f) | expr | top1000 | 0.00 | 24 | -2.99 |
| [Quality Momentum Top 1000](https://data.finzdata.com/s/quality-momentum-top-1000-e3a4ec) | expr | top1000 | 0.00 | 25 | -2.32 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-00b358) | expr | top1000 | 0.00 | 25 | -3.47 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-104f2b) | expr | top1000 | 0.00 | 23 | -5.56 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-352f87) | expr | top1000 | 0.00 | 24 | -4.42 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-528e4c) | expr | top1000 | 0.00 | 25 | -3.08 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-7005a5) | expr | top1000 | 0.00 | 25 | -3.47 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-7d1e3c) | expr | top1000 | 0.00 | 24 | -4.42 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-823db4) | expr | top1000 | 0.00 | 24 | -2.42 |
| [Quality-Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-bc6cd3) | expr | top1000 | 0.00 | 23 | -0.69 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-cf38eb) | expr | top1000 | 0.00 | 24 | -2.43 |
| [Quality Momentum Top 1000 Long Only](https://data.finzdata.com/s/quality-momentum-top-1000-long-only-da6f93) | expr | top1000 | 0.00 | 25 | -3.47 |
| [Quality Momentum Top 1000 Quarterly](https://data.finzdata.com/s/quality-momentum-top-1000-quarterly-044f7b) | expr | top1000 | 0.00 | 25 | -4.52 |
| [Quality Momentum Top1000 Long Only](https://data.finzdata.com/s/quality-momentum-top1000-long-only-b1c04b) | expr | top1000 | 0.00 | 26 | 0.08 |
| [Quality Momentum Top1000 Long Only](https://data.finzdata.com/s/quality-momentum-top1000-long-only-de4627) | expr | top1000 | 0.00 | 25 | -3.47 |
| [Quality Ranked Momentum with Earnings Yield Screen](https://data.finzdata.com/s/quality-ranked-momentum-with-earnings-yield-screen-3cc7ab) | expr | top1000 | 0.00 | 26 | -0.95 |
| [Quality ROE minus leverage · top 50 · Top 1000 · long only](https://data.finzdata.com/s/quality-roe-minus-leverage-top-50-top-1000-long-only-53c7ed) | expr | top1000 | 0.00 | 25 | -4.39 |
| [Quality ROE minus leverage · top 50 · Top 1000 · long only](https://data.finzdata.com/s/quality-roe-minus-leverage-top-50-top-1000-long-only-acc176) | expr | top1000 | 0.00 | 25 | -2.49 |
| [Quality ROE minus leverage · top 50 · Top 500 · long/short](https://data.finzdata.com/s/quality-roe-minus-leverage-top-50-top-500-long-short-66c523) | expr | top500 | 0.00 | 19 | 0.25 |
| [Quality screen high ROE low leverage · top 50 · Top 1000 · long only](https://data.finzdata.com/s/quality-screen-high-roe-low-leverage-top-50-top-1000-long-on-a2202a) | expr | top1000 | 0.00 | 24 | -5.13 |
| [Quality-Size Tilt · Top 1000 · Long Only](https://data.finzdata.com/s/quality-size-tilt-top-1000-long-only-0589e2) | expr | top1000 | 0.00 | 25 | -3.23 |
| [Quality Value Blend · earnings yield and book-to-market rank](https://data.finzdata.com/s/quality-value-blend-earnings-yield-and-book-to-market-rank-2161f9) | expr | top1000 | 0.00 | 8 | -15.28 |
| [Quality Value Composite Rank Long Only](https://data.finzdata.com/s/quality-value-composite-rank-long-only-4ae60a) | expr | top1000 | 0.00 | 23 | -7.56 |
| [Quality Value Composite Rank Top 100](https://data.finzdata.com/s/quality-value-composite-rank-top-100-160260) | expr | top1000 | 0.00 | 16 | -9.45 |
| [Quality value with balance-sheet resilience](https://data.finzdata.com/s/quality-value-with-balance-sheet-resilience-65ed01) | expr | top3000 | 0.00 | 25 | -3.26 |
| [Return‑RoE Hybrid](https://data.finzdata.com/s/return-roe-hybrid-a7792d) | expr | top500 | 0.00 | 19 | -4.46 |
| [ROE Quality Factor Long Only Top 500](https://data.finzdata.com/s/roe-quality-factor-long-only-top-500-640133) | expr | top500 | 0.00 | 9 | -13.18 |
| [RSI 14 oversold reversal · top 50 · Top 1000 · long only](https://data.finzdata.com/s/rsi-14-oversold-reversal-top-50-top-1000-long-only-e8cd43) | expr | top1000 | 0.00 | 25 | 1.65 |
| [RSI 14 oversold tilt · top 50 · Top 500 · long only](https://data.finzdata.com/s/rsi-14-oversold-tilt-top-50-top-500-long-only-9ebba0) | expr | top500 | 0.00 | 19 | -1.57 |
| [RSI Mean Reversion 14d · top 100 · Top 500 · long_short](https://data.finzdata.com/s/rsi-mean-reversion-14d-top-100-top-500-long-short-9861e1) | expr | top500 | 0.00 | 20 | -0.13 |
| [RSI Mean Reversion 14d · Top 50 · Top 1000 · Long/Short](https://data.finzdata.com/s/rsi-mean-reversion-14d-top-50-top-1000-long-short-c757dd) | expr | top1000 | 0.00 | 31 | -1.50 |
| [RSI Mean Reversion 14D · Top 500 · Long/Short](https://data.finzdata.com/s/rsi-mean-reversion-14d-top-500-long-short-fb4350) | expr | top500 | 0.00 | 16 | 2.47 |
| [RSI Mean Reversion 20D \| top500 \| long_short](https://data.finzdata.com/s/rsi-mean-reversion-20d-top500-long-short-a2203c) | expr | top500 | 0.00 | 19 | 0.65 |
| [RSI mean reversion expr long only](https://data.finzdata.com/s/rsi-mean-reversion-expr-long-only-a658f3) | expr | top500 | 0.00 | 8 | -12.61 |
| [RSI Mean Reversion Expr Top 500](https://data.finzdata.com/s/rsi-mean-reversion-expr-top-500-ad1587) | expr | top500 | 0.00 | 14 | 1.19 |
| [RSI Mean Reversion Expr Top 500](https://data.finzdata.com/s/rsi-mean-reversion-expr-top-500-e6df4f) | expr | top500 | 0.00 | 14 | -1.66 |
| [RSI Mean Reversion Expression Long Short](https://data.finzdata.com/s/rsi-mean-reversion-expression-long-short-7c551b) | expr | top1000 | 0.00 | 6 | -11.13 |
| [RSI Mean Reversion Long Only Top 500 Weekly](https://data.finzdata.com/s/rsi-mean-reversion-long-only-top-500-weekly-fc3f93) | expr | top500 | 0.00 | 19 | 1.18 |
| [RSI Mean Reversion Long Short](https://data.finzdata.com/s/rsi-mean-reversion-long-short-ee2a25) | expr | top500 | 0.00 | 19 | 0.65 |
| [RSI Mean Reversion Momentum Top 1000](https://data.finzdata.com/s/rsi-mean-reversion-momentum-top-1000-6c9610) | expr | top1000 | 0.00 | 25 | 1.16 |
| [RSI Mean Reversion Oversold Top 500 Long Only](https://data.finzdata.com/s/rsi-mean-reversion-oversold-top-500-long-only-6da821) | expr | top500 | 0.00 | 18 | -0.23 |
| [RSI Mean Reversion Top 1000](https://data.finzdata.com/s/rsi-mean-reversion-top-1000-ed29b5) | expr | top1000 | 0.00 | 25 | 0.81 |
| [RSI Mean Reversion Top 500 Long Only](https://data.finzdata.com/s/rsi-mean-reversion-top-500-long-only-ecbae7) | expr | top500 | 0.00 | 19 | -3.59 |
| [RSI Mean Reversion Top500 Long Only](https://data.finzdata.com/s/rsi-mean-reversion-top500-long-only-37855a) | expr | top500 | 0.00 | 19 | 1.09 |
| [RSI Mean Reversion Weekly Top 500 Long Only](https://data.finzdata.com/s/rsi-mean-reversion-weekly-top-500-long-only-d41115) | expr | top500 | 0.00 | 19 | 1.23 |
| [RSI‑Weighted Momentum](https://data.finzdata.com/s/rsi-weighted-momentum-7fb3b5) | expr | top500 | 0.00 | 19 | -10.65 |
| [Sales-yield conditioned intermediate momentum](https://data.finzdata.com/s/sales-yield-conditioned-intermediate-momentum-1bbeea) | expr | top3000 | 0.00 | 25 | 0.13 |
| [Sales Yield + Low Volatility Factor](https://data.finzdata.com/s/sales-yield-low-volatility-factor-0666f0) | expr | top500 | 0.00 | 18 | -0.87 |
| [Sales Yield Momentum 126d skip 21 top 40 Top 500 long only](https://data.finzdata.com/s/sales-yield-momentum-126d-skip-21-top-40-top-500-long-only-e8f866) | expr | top500 | 0.00 | 17 | -3.99 |
| [Sales Yield Momentum 12M Skip 1M · Top 40 · Top 1000 · Long Only](https://data.finzdata.com/s/sales-yield-momentum-12m-skip-1m-top-40-top-1000-long-only-35ff16) | expr | top1000 | 0.00 | 31 | -2.24 |
| [Sales Yield Momentum 12M Skip 1M · Top 40 · Top 500 · Long Only](https://data.finzdata.com/s/sales-yield-momentum-12m-skip-1m-top-40-top-500-long-only-ec27d4) | expr | top500 | 0.00 | 19 | -3.49 |
| [Sales Yield Momentum 12M Skip 1M Top 40 Top1000 Long Only](https://data.finzdata.com/s/sales-yield-momentum-12m-skip-1m-top-40-top1000-long-only-54b519) | expr | top1000 | 0.00 | 25 | -2.51 |
| [Sales Yield Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/sales-yield-momentum-12m-skip-1m-top-50-top-1000-long-only-4e1a5b) | expr | top1000 | 0.00 | 25 | -1.88 |
| [Sales Yield Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/sales-yield-momentum-12m-skip-1m-top-50-top-1000-long-only-5a0203) | expr | top1000 | 0.00 | 24 | -2.39 |
| [Sales Yield Momentum 12M \| Top 1000 \| Long Only](https://data.finzdata.com/s/sales-yield-momentum-12m-top-1000-long-only-9f35b8) | expr | top1000 | 0.00 | 20 | -4.89 |
| [Sales Yield Momentum 252d skip 21 · top 40 · Top 500 · long only](https://data.finzdata.com/s/sales-yield-momentum-252d-skip-21-top-40-top-500-long-only-926ede) | expr | top500 | 0.00 | 20 | -3.34 |
| [Sales Yield Momentum 63d skip 0 · top 30 · Top 500 · long/short](https://data.finzdata.com/s/sales-yield-momentum-63d-skip-0-top-30-top-500-long-short-863af3) | expr | top500 | 0.00 | 16 | 3.72 |
| [Sales Yield Momentum · 6m change in sales yield](https://data.finzdata.com/s/sales-yield-momentum-6m-change-in-sales-yield-ff6b81) | expr | top1000 | 0.00 | 3 | -7.05 |
| [Sales Yield Momentum 6M Skip 1M · Top 30 · Top 1000 · Long/Short](https://data.finzdata.com/s/sales-yield-momentum-6m-skip-1m-top-30-top-1000-long-short-0b2c8a) | expr | top1000 | 0.00 | 25 | 1.72 |
| [Sales Yield Momentum 9M Skip 1M · Top 50 · Top 500 · Long Only](https://data.finzdata.com/s/sales-yield-momentum-9m-skip-1m-top-50-top-500-long-only-622cba) | expr | top500 | 0.00 | 19 | -3.75 |
| [Sales-yield momentum blend · top 500 · monthly](https://data.finzdata.com/s/sales-yield-momentum-blend-top-500-monthly-0e5d8e) | expr | top500 | 0.00 | 19 | -3.92 |
| [Sales Yield Momentum Top 1000](https://data.finzdata.com/s/sales-yield-momentum-top-1000-17386c) | expr | top1000 | 0.00 | 25 | -2.00 |
| [Sales Yield Momentum Top 1000 Long Only](https://data.finzdata.com/s/sales-yield-momentum-top-1000-long-only-a2cb9d) | expr | top1000 | 0.00 | 25 | -2.95 |
| [Sales Yield Momentum Top 1000 Long Only](https://data.finzdata.com/s/sales-yield-momentum-top-1000-long-only-ee9d5f) | expr | top1000 | 0.00 | 24 | -0.28 |
| [Sales Yield Momentum Top 3000 Long Only](https://data.finzdata.com/s/sales-yield-momentum-top-3000-long-only-616045) | expr | top3000 | 0.00 | 25 | 1.50 |
| [Sales Yield Momentum Top 500 Long Only](https://data.finzdata.com/s/sales-yield-momentum-top-500-long-only-a4deb5) | expr | top500 | 0.00 | 19 | -3.81 |
| [Sales Yield Quality Momentum Blend](https://data.finzdata.com/s/sales-yield-quality-momentum-blend-c29154) | expr | top3000 | 0.00 | 25 | -1.09 |
| [Sales Yield Trend Following](https://data.finzdata.com/s/sales-yield-trend-following-e81a86) | expr | top1000 | 0.00 | 19 | 2.02 |
| [Sales Yield Value Factor Top 500](https://data.finzdata.com/s/sales-yield-value-factor-top-500-f1ad5f) | expr | top500 | 0.00 | 9 | -12.93 |
| [Sales Yield Value · sales_yield rank with volatility demean](https://data.finzdata.com/s/sales-yield-value-sales-yield-rank-with-volatility-demean-c0c198) | expr | top1000 | 0.00 | 8 | -4.36 |
| [Sales Yield Value Top 3000](https://data.finzdata.com/s/sales-yield-value-top-3000-dbde47) | expr | top3000 | 0.00 | 25 | -1.49 |
| [Seasonal Momentum · 252d skip 21 · Top 500 · Long Only](https://data.finzdata.com/s/seasonal-momentum-252d-skip-21-top-500-long-only-7a4ffb) | expr | top500 | 0.00 | 0 | 0.00 |
| [Seasonal Momentum November-April Top 500 Long Only](https://data.finzdata.com/s/seasonal-momentum-november-april-top-500-long-only-4851ba) | momentum | top500 | 0.00 | 18 | -6.53 |
| [Short-Term Reversal 1M · Liquid Stocks · Top 100 · Top 1000 · Long/Short](https://data.finzdata.com/s/short-term-reversal-1m-liquid-stocks-top-100-top-1000-long-s-672d6c) | momentum | top1000 | 0.00 | 25 | 1.00 |
| [Short-term reversal · 21d losers · top 50 · Top 1000 · long only · weekly](https://data.finzdata.com/s/short-term-reversal-21d-losers-top-50-top-1000-long-only-wee-26d8bd) | expr | top1000 | 0.00 | 25 | 0.22 |
| [Short-term reversal 21d · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/short-term-reversal-21d-top-50-top-1000-long-short-6915e3) | expr | top1000 | 0.00 | 25 | -1.56 |
| [Short-Term Reversal 5D Skip 1D Long/Short Top500](https://data.finzdata.com/s/short-term-reversal-5d-skip-1d-long-short-top500-7baa8f) | expr | top500 | 0.00 | 19 | 2.78 |
| [Short-Term Reversal 5D Skip 1D · Top 100 · Top 3000 · Long/Short](https://data.finzdata.com/s/short-term-reversal-5d-skip-1d-top-100-top-3000-long-short-8daa6d) | expr | top3000 | 0.00 | 25 | -1.75 |
| [Short-Term Reversal 5d · Top 100 · Top 1000 · Long/Short](https://data.finzdata.com/s/short-term-reversal-5d-top-100-top-1000-long-short-1c2abe) | expr | top1000 | 0.00 | 25 | 2.79 |
| [Short-Term Reversal 5d · Top 100 · Top 1000 · Long/Short](https://data.finzdata.com/s/short-term-reversal-5d-top-100-top-1000-long-short-b93419) | expr | top1000 | 0.00 | 31 | 0.35 |
| [Short-Term Reversal 5D · Top 1000 · Long/Short](https://data.finzdata.com/s/short-term-reversal-5d-top-1000-long-short-0cc498) | expr | top1000 | 0.00 | 22 | 0.51 |
| [Short-term reversal 5d · top 50 · Top 500 · long/short](https://data.finzdata.com/s/short-term-reversal-5d-top-50-top-500-long-short-f7c80b) | expr | top500 | 0.00 | 19 | 3.41 |
| [Short-Term Reversal Factor Neutral Top 1000 Long/Short](https://data.finzdata.com/s/short-term-reversal-factor-neutral-top-1000-long-short-cd7fb3) | expr | top1000 | 0.00 | 25 | -3.32 |
| [Short-Term Reversal Liquid Top 500 Long/Short](https://data.finzdata.com/s/short-term-reversal-liquid-top-500-long-short-432272) | expr | top500 | 0.00 | 18 | 2.91 |
| [Short-Term Reversal Long/Short Top 500](https://data.finzdata.com/s/short-term-reversal-long-short-top-500-5b4a5a) | expr | top500 | 0.00 | 19 | -1.97 |
| [Short-Term Reversal Long/Short Top 500 Weekly](https://data.finzdata.com/s/short-term-reversal-long-short-top-500-weekly-eb5077) | expr | top500 | 0.00 | 19 | 4.12 |
| [Short-term Reversal Top 500 Long/Short](https://data.finzdata.com/s/short-term-reversal-top-500-long-short-434b66) | momentum | top500 | 0.00 | 19 | 1.68 |
| [Short-Term Reversal Top 500 Long/Short](https://data.finzdata.com/s/short-term-reversal-top-500-long-short-ff1bd9) | expr | top500 | 0.00 | 18 | -3.40 |
| [Short-term reversal · top1000 · long short](https://data.finzdata.com/s/short-term-reversal-top1000-long-short-d9bbf7) | expr | top1000 | 0.00 | 24 | 1.03 |
| [Short-Term Reversal Top500 Long Short](https://data.finzdata.com/s/short-term-reversal-top500-long-short-1c4898) | expr | top500 | 0.00 | 19 | -1.93 |
| [Short-term reversal with quality · top 1000 · weekly](https://data.finzdata.com/s/short-term-reversal-with-quality-top-1000-weekly-097b73) | expr | top1000 | 0.00 | 25 | -0.69 |
| [Size Adjusted EBITDA EV Value](https://data.finzdata.com/s/size-adjusted-ebitda-ev-value-7d0ae9) | expr | top3000 | 0.00 | 19 | -3.52 |
| [Size-Adjusted Momentum 6M Skip 1M · Top 50 · Top 3000 · Long Only](https://data.finzdata.com/s/size-adjusted-momentum-6m-skip-1m-top-50-top-3000-long-only-2fc737) | expr | top3000 | 0.00 | 25 | 0.17 |
| [Size adjusted sales yield factor](https://data.finzdata.com/s/size-adjusted-sales-yield-factor-207fff) | expr | top3000 | 0.00 | 3 | -7.37 |
| [Size Adjusted Value · book to market over log market cap](https://data.finzdata.com/s/size-adjusted-value-book-to-market-over-log-market-cap-f587c9) | expr | top3000 | 0.00 | 3 | -11.66 |
| [Size adjusted value using log market cap and book to market](https://data.finzdata.com/s/size-adjusted-value-using-log-market-cap-and-book-to-market-3ffcbd) | expr | top3000 | 0.00 | 6 | -7.42 |
| [Size factor · top3000 · long short](https://data.finzdata.com/s/size-factor-top3000-long-short-31bf67) | expr | top3000 | 0.00 | 24 | -1.44 |
| [Size-Momentum 12M Skip 1M · Top 50 · Top 3000 · Long Only](https://data.finzdata.com/s/size-momentum-12m-skip-1m-top-50-top-3000-long-only-824902) | expr | top3000 | 0.00 | 24 | -0.50 |
| [Size · smallest 100 of the Top 1000 by market value (after Banz 1981) · long/short](https://data.finzdata.com/s/size-smallest-100-of-the-top-1000-by-market-value-after-banz-435aa0) | factor | top1000 | 0.00 | 33 | 3.15 |
| [Size · smallest 100 of the Top 1000 by market value (after Banz 1981) · long only](https://data.finzdata.com/s/size-smallest-100-of-the-top-1000-by-market-value-after-banz-884945) | factor | top1000 | 0.00 | 33 | 2.38 |
| [Slow trend 100-300 · top 3000 · weekly](https://data.finzdata.com/s/slow-trend-100-300-top-3000-weekly-0b703e) | ma_cross | top3000 | 0.00 | 25 | 0.79 |
| [Slow Trend Crossover 100/300 · Top 500 · Long Only](https://data.finzdata.com/s/slow-trend-crossover-100-300-top-500-long-only-f95443) | ma_cross | top500 | 0.00 | 19 | -2.18 |
| [Small-cap earnings-yield value](https://data.finzdata.com/s/small-cap-earnings-yield-value-b601da) | expr | top3000 | 0.00 | 25 | -1.60 |
| [Small-Cap Momentum · 126d skip 21 · Microcap universe · Long Only](https://data.finzdata.com/s/small-cap-momentum-126d-skip-21-microcap-universe-long-only-c74dee) | momentum | top3000 | 0.00 | 7 | -14.76 |
| [Small Cap Momentum 12M Skip 1M · Microcap Excluded · Top 100 · Top 3000 · Long Only](https://data.finzdata.com/s/small-cap-momentum-12m-skip-1m-microcap-excluded-top-100-top-afaa34) | expr | top3000 | 0.00 | 25 | -5.21 |
| [Small Cap Momentum 12M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/small-cap-momentum-12m-skip-1m-top-50-top-1000-long-only-433ca8) | expr | top1000 | 0.00 | 25 | -3.03 |
| [Small-Cap Momentum · 63d skip 0 · Top 3000 · Long Only](https://data.finzdata.com/s/small-cap-momentum-63d-skip-0-top-3000-long-only-505956) | momentum | top3000 | 0.00 | 0 | 0.00 |
| [Small Cap Momentum 9M Skip 1M · Top 50 · Top 3000 · Long Only](https://data.finzdata.com/s/small-cap-momentum-9m-skip-1m-top-50-top-3000-long-only-271945) | expr | top3000 | 0.00 | 24 | -0.75 |
| [Small Cap Momentum Top 3000 Long Only](https://data.finzdata.com/s/small-cap-momentum-top-3000-long-only-390f86) | momentum | top3000 | 0.00 | 24 | -5.20 |
| [Small Cap Momentum Top 3000 Long Only](https://data.finzdata.com/s/small-cap-momentum-top-3000-long-only-aebca2) | expr | top3000 | 0.00 | 24 | -1.39 |
| [Small Cap Momentum Top 3000 Monthly](https://data.finzdata.com/s/small-cap-momentum-top-3000-monthly-76d76b) | momentum | top3000 | 0.00 | 25 | -4.19 |
| [Small-cap value · book-to-market minus log size · top 100 · Top 3000 · long only](https://data.finzdata.com/s/small-cap-value-book-to-market-minus-log-size-top-100-top-30-849851) | expr | top3000 | 0.00 | 25 | -0.93 |
| [Small Cap Value Momentum Monthly Top 3000](https://data.finzdata.com/s/small-cap-value-momentum-monthly-top-3000-7c9242) | expr | top3000 | 0.00 | 25 | -0.83 |
| [Small Cap Value Momentum Top 3000](https://data.finzdata.com/s/small-cap-value-momentum-top-3000-177831) | expr | top3000 | 0.00 | 25 | 1.22 |
| [Small-cap value tilt · top 50 · Top 3000 · long only](https://data.finzdata.com/s/small-cap-value-tilt-top-50-top-3000-long-only-9a20e2) | expr | top3000 | 0.00 | 25 | 2.58 |
| [SPY 20/100 moving-average trend](https://data.finzdata.com/s/spy-20-100-moving-average-trend-0c0d51) | ma_cross | custom | 0.00 | 28 | -0.93 |
| [SPY 50/200 moving-average trend](https://data.finzdata.com/s/spy-50-200-moving-average-trend-417064) | ma_cross | custom | 0.00 | 28 | -4.27 |
| [Time-series momentum 1-month · 16 asset ETFs (after Moskowitz, Ooi & Pedersen 2012, AQR)](https://data.finzdata.com/s/time-series-momentum-1-month-16-asset-etfs-after-moskowitz-o-f9bd3d) | expr | custom | 0.00 | 28 | -5.76 |
| [Time-series momentum 12-month · 16 asset ETFs (after Moskowitz, Ooi & Pedersen 2012, AQR)](https://data.finzdata.com/s/time-series-momentum-12-month-16-asset-etfs-after-moskowitz-1018a6) | expr | custom | 0.00 | 28 | -5.68 |
| [Time-series momentum 3-month · 16 asset ETFs (after Moskowitz, Ooi & Pedersen 2012, AQR)](https://data.finzdata.com/s/time-series-momentum-3-month-16-asset-etfs-after-moskowitz-o-aeea42) | expr | custom | 0.00 | 28 | -5.26 |
| [TLT 20/100 moving-average trend](https://data.finzdata.com/s/tlt-20-100-moving-average-trend-9a79b0) | ma_cross | custom | 0.00 | 28 | 0.00 |
| [TLT 50/200 moving-average trend](https://data.finzdata.com/s/tlt-50-200-moving-average-trend-a66590) | ma_cross | custom | 0.00 | 28 | 0.00 |
| [US Dividend Yield Quality Filter](https://data.finzdata.com/s/us-dividend-yield-quality-filter-e46852) | expr | top1000 | 0.00 | 0 | 0.00 |
| [US Earnings Momentum · 3m Price Momentum + Earnings Yield Change · Top 1000 · Long Only](https://data.finzdata.com/s/us-earnings-momentum-3m-price-momentum-earnings-yield-change-255c24) | expr | top1000 | 0.00 | 0 | 0.00 |
| [US EBITDA/EV Value with Sales Yield](https://data.finzdata.com/s/us-ebitda-ev-value-with-sales-yield-9a22d5) | expr | top1000 | 0.00 | 0 | 0.00 |
| [US Large Cap 20/100 MA Cross Trend Filter](https://data.finzdata.com/s/us-large-cap-20-100-ma-cross-trend-filter-06d027) | ma_cross | top500 | 0.00 | 6 | 1.21 |
| [US Low Leverage Quality · book-to-market minus leverage](https://data.finzdata.com/s/us-low-leverage-quality-book-to-market-minus-leverage-ad26f9) | expr | top500 | 0.00 | 0 | 0.00 |
| [US Low-Volatility Momentum · 9m Momentum / 1y Vol · Top 500 · Long Only](https://data.finzdata.com/s/us-low-volatility-momentum-9m-momentum-1y-vol-top-500-long-o-1c7508) | expr | top500 | 0.00 | 0 | 0.00 |
| [US Profitability Change · 12-month ROE momentum](https://data.finzdata.com/s/us-profitability-change-12-month-roe-momentum-89afbf) | expr | top1000 | 0.00 | 0 | 0.00 |
| [US Quality-Momentum · 6m Momentum + ROE · Top 1000 · Long Only](https://data.finzdata.com/s/us-quality-momentum-6m-momentum-roe-top-1000-long-only-733a08) | expr | top1000 | 0.00 | 0 | 0.00 |
| [US Quality Value Blend · earnings yield + ROE composite](https://data.finzdata.com/s/us-quality-value-blend-earnings-yield-roe-composite-7de7fc) | expr | top1000 | 0.00 | 4 | -51.35 |
| [US Quality-Value Composite · earnings yield minus leverage](https://data.finzdata.com/s/us-quality-value-composite-earnings-yield-minus-leverage-6f6e7c) | expr | top1000 | 0.00 | 0 | 0.00 |
| [US Quality-Value Composite Factor Long Only](https://data.finzdata.com/s/us-quality-value-composite-factor-long-only-a8a3ad) | expr | top1000 | 0.00 | 6 | -17.22 |
| [US Seasonal Momentum · Nov-Apr 6m Momentum · Top 500 · Long Only](https://data.finzdata.com/s/us-seasonal-momentum-nov-apr-6m-momentum-top-500-long-only-f22bad) | momentum | top500 | 0.00 | 0 | 0.00 |
| [US Size-Adjusted Earnings Yield](https://data.finzdata.com/s/us-size-adjusted-earnings-yield-bb27a5) | expr | top3000 | 0.00 | 0 | 0.00 |
| [US Value-Momentum Blend · 12m Momentum + Earnings Yield · Top 500 · Long Only](https://data.finzdata.com/s/us-value-momentum-blend-12m-momentum-earnings-yield-top-500-6ba7a2) | expr | top500 | 0.00 | 0 | 0.00 |
| [US Value with Earnings Yield and Book-to-Market](https://data.finzdata.com/s/us-value-with-earnings-yield-and-book-to-market-a76f49) | expr | top1000 | 0.00 | 0 | 0.00 |
| [Value-Adjusted Momentum](https://data.finzdata.com/s/value-adjusted-momentum-ceb25c) | expr | top500 | 0.00 | 18 | -4.45 |
| [Value and momentum · book-to-market plus 12-1 momentum · top 50 of the Top 500 (after Asness, Moskowitz & Pedersen 2013, AQR) · long only](https://data.finzdata.com/s/value-and-momentum-book-to-market-plus-12-1-momentum-top-50-acca74) | expr | top500 | 0.00 | 27 | -3.10 |
| [Value and momentum · book-to-market plus 12-1 momentum · top 50 of the Top 500 (after Asness, Moskowitz & Pedersen 2013, AQR) · long/short](https://data.finzdata.com/s/value-and-momentum-book-to-market-plus-12-1-momentum-top-50-feb8ea) | expr | top500 | 0.00 | 27 | -1.00 |
| [Value book_to_market rank · top 50 · Top 1000 · long only](https://data.finzdata.com/s/value-book-to-market-rank-top-50-top-1000-long-only-2f96b9) | expr | top1000 | 0.00 | 25 | -2.58 |
| [Value · book-to-market · top 50 of the Top 500 (after Fama & French 1992, the HML factor) · long/short](https://data.finzdata.com/s/value-book-to-market-top-50-of-the-top-500-after-fama-french-5fa648) | factor | top500 | 0.00 | 27 | 1.72 |
| [Value · book-to-market · top 50 of the Top 500 (after Fama & French 1992, the HML factor) · long only](https://data.finzdata.com/s/value-book-to-market-top-50-of-the-top-500-after-fama-french-b81beb) | factor | top500 | 0.00 | 27 | -2.42 |
| [Value composite: BM + EY + ROE · top1000 · long only](https://data.finzdata.com/s/value-composite-bm-ey-roe-top1000-long-only-0e93a5) | expr | top1000 | 0.00 | 24 | 3.51 |
| [Value composite · earnings yield + book-to-market · top 50 · Top 500 · long only](https://data.finzdata.com/s/value-composite-earnings-yield-book-to-market-top-50-top-500-cca8cf) | expr | top500 | 0.00 | 19 | -1.65 |
| [Value composite EY+BM · top 50 · Top 500 · long only](https://data.finzdata.com/s/value-composite-ey-bm-top-50-top-500-long-only-ed1adb) | expr | top500 | 0.00 | 19 | -2.39 |
| [Value composite EY+BTM · top 50 · Top 1000 · long only](https://data.finzdata.com/s/value-composite-ey-btm-top-50-top-1000-long-only-4edfa6) | expr | top1000 | 0.00 | 25 | 2.07 |
| [Value composite EY+BTM · top 50 · Top 1000 · long/short](https://data.finzdata.com/s/value-composite-ey-btm-top-50-top-1000-long-short-249dc3) | expr | top1000 | 0.00 | 24 | 3.91 |
| [Value · earnings yield · top 50 of the Top 500 (after Basu 1977; Fama & French 1992) · long only](https://data.finzdata.com/s/value-earnings-yield-top-50-of-the-top-500-after-basu-1977-f-7d5a40) | factor | top500 | 0.00 | 27 | -1.24 |
| [Value · earnings yield · top 50 of the Top 500 (after Basu 1977; Fama & French 1992) · long/short](https://data.finzdata.com/s/value-earnings-yield-top-50-of-the-top-500-after-basu-1977-f-e6ad2f) | factor | top500 | 0.00 | 27 | 2.69 |
| [Value · EBITDA to enterprise value · top 50 of the Top 500 (after Loughran & Wellman 2011) · long only](https://data.finzdata.com/s/value-ebitda-to-enterprise-value-top-50-of-the-top-500-after-28b33a) | factor | top500 | 0.00 | 27 | -0.90 |
| [Value · EBITDA to enterprise value · top 50 of the Top 500 (after Loughran & Wellman 2011) · long/short](https://data.finzdata.com/s/value-ebitda-to-enterprise-value-top-50-of-the-top-500-after-381df4) | factor | top500 | 0.00 | 27 | 3.16 |
| [Value Momentum 12M Skip 1M Top 40 Top1000 Long Only](https://data.finzdata.com/s/value-momentum-12m-skip-1m-top-40-top1000-long-only-a4b939) | expr | top1000 | 0.00 | 23 | -2.39 |
| [Value Momentum · 252d skip 21 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-252d-skip-21-top-1000-long-only-75baed) | expr | top1000 | 0.00 | 0 | 0.00 |
| [Value-Momentum Blend 126d · top 50 · Top 500 · long only](https://data.finzdata.com/s/value-momentum-blend-126d-top-50-top-500-long-only-aad4a6) | expr | top500 | 0.00 | 20 | -3.43 |
| [Value-Momentum Blend 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-blend-12m-1m-skip-1m-top-50-top-1000-long-onl-57bfb6) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Value-Momentum Blend 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-blend-12m-1m-skip-1m-top-50-top-1000-long-onl-6e325d) | expr | top1000 | 0.00 | 22 | -4.75 |
| [Value-Momentum Blend 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-blend-12m-1m-skip-1m-top-50-top-1000-long-onl-9f3215) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Value-Momentum Blend 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-blend-12m-1m-skip-1m-top-50-top-1000-long-onl-bb29af) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Value-Momentum Blend 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-blend-12m-1m-skip-1m-top-50-top-1000-long-onl-ec1d52) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Value-Momentum Blend · 12m momentum + earnings yield · Top 1000 · Long-Short](https://data.finzdata.com/s/value-momentum-blend-12m-momentum-earnings-yield-top-1000-lo-3e459c) | expr | top1000 | 0.00 | 5 | 5.55 |
| [Value-Momentum Blend 12M Skip 1M Top 50 Top500 Long Only](https://data.finzdata.com/s/value-momentum-blend-12m-skip-1m-top-50-top500-long-only-c6f335) | expr | top500 | 0.00 | 19 | -5.06 |
| [Value-Momentum Blend · 252d skip 21 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-blend-252d-skip-21-top-1000-long-only-40e366) | expr | top1000 | 0.00 | 0 | 0.00 |
| [Value-Momentum Blend Top 500 Monthly](https://data.finzdata.com/s/value-momentum-blend-top-500-monthly-4d4fcc) | expr | top500 | 0.00 | 19 | -5.06 |
| [Value-Momentum Blend Top500 Long Only](https://data.finzdata.com/s/value-momentum-blend-top500-long-only-abe2be) | expr | top500 | 0.00 | 20 | -4.40 |
| [Value-Momentum Combo 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-combo-12m-1m-skip-1m-top-50-top-1000-long-onl-5e1007) | expr | top1000 | 0.00 | 24 | -2.87 |
| [Value-Momentum Combo 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-combo-12m-1m-skip-1m-top-50-top-1000-long-onl-939d99) | expr | top1000 | 0.00 | 31 | 1.81 |
| [Value-Momentum Combo 12M/1M Skip 1M · Top 50 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-combo-12m-1m-skip-1m-top-50-top-1000-long-onl-fe626b) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Value Momentum Combo 12M Skip 1M Top 50 Top500 Long Only](https://data.finzdata.com/s/value-momentum-combo-12m-skip-1m-top-50-top500-long-only-0ee458) | expr | top500 | 0.00 | 20 | -4.40 |
| [Value Momentum Combo 252d skip 21 top 50 Top 500 long only](https://data.finzdata.com/s/value-momentum-combo-252d-skip-21-top-50-top-500-long-only-972ff1) | expr | top500 | 0.00 | 17 | -4.87 |
| [Value momentum combo BTM + 12-1 ret · top 50 · Top 1000 · long only](https://data.finzdata.com/s/value-momentum-combo-btm-12-1-ret-top-50-top-1000-long-only-cc6036) | expr | top1000 | 0.00 | 24 | -1.55 |
| [Value-Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-44bc81) | expr | top500 | 0.00 | 19 | -4.58 |
| [Value-Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-46e1e2) | expr | top500 | 0.00 | 17 | -4.87 |
| [Value Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-52edf7) | expr | top500 | 0.00 | 18 | -4.69 |
| [Value Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-6392e9) | expr | top500 | 0.00 | 18 | -4.69 |
| [Value-Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-a34a6f) | expr | top500 | 0.00 | 19 | -4.58 |
| [Value-Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-c5477a) | expr | top500 | 0.00 | 18 | -4.69 |
| [Value-Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-ca1252) | expr | top500 | 0.00 | 17 | -4.87 |
| [Value Momentum Combo Top 500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top-500-long-only-d2eb6e) | expr | top500 | 0.00 | 19 | -5.71 |
| [Value-Momentum Combo Top500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top500-long-only-55b726) | expr | top500 | 0.00 | 14 | -4.84 |
| [Value Momentum Combo Top500 Long Only](https://data.finzdata.com/s/value-momentum-combo-top500-long-only-b00cd2) | expr | top500 | 0.00 | 19 | -4.58 |
| [Value-Momentum Composite 252d/21d Skip · Top 30 · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-composite-252d-21d-skip-top-30-top-1000-long-58d802) | expr | top1000 | 0.00 | 25 | -2.12 |
| [Value Momentum Composite Earnings Yield Book to Market](https://data.finzdata.com/s/value-momentum-composite-earnings-yield-book-to-market-1f95eb) | expr | top1000 | 0.00 | 19 | -4.80 |
| [Value-Momentum Composite · Top 1000 · Long Only](https://data.finzdata.com/s/value-momentum-composite-top-1000-long-only-412b54) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Value Momentum Composite with Book to Market](https://data.finzdata.com/s/value-momentum-composite-with-book-to-market-3b365a) | expr | top1000 | 0.00 | 26 | 3.00 |
| [Value-Momentum Hybrid Top 500](https://data.finzdata.com/s/value-momentum-hybrid-top-500-7c8dc0) | expr | top500 | 0.00 | 18 | -6.67 |
| [Value Momentum Hybrid Top 500](https://data.finzdata.com/s/value-momentum-hybrid-top-500-bb944b) | expr | top500 | 0.00 | 9 | -14.48 |
| [Value-Momentum Hybrid Top 500](https://data.finzdata.com/s/value-momentum-hybrid-top-500-be65df) | expr | top500 | 0.00 | 19 | -6.22 |
| [Value momentum hybrid using earnings yield and 126d return](https://data.finzdata.com/s/value-momentum-hybrid-using-earnings-yield-and-126d-return-6dd3ee) | expr | top1000 | 0.00 | 4 | -25.97 |
| [Value Momentum Intersection Long Only](https://data.finzdata.com/s/value-momentum-intersection-long-only-c6ea7d) | expr | top500 | 0.00 | 14 | -7.69 |
| [Value plus momentum blend · top 50 · Top 1000 · long only](https://data.finzdata.com/s/value-plus-momentum-blend-top-50-top-1000-long-only-d98b8d) | expr | top1000 | 0.00 | 25 | -2.25 |
| [Value quality composite · earnings yield + ROE screen](https://data.finzdata.com/s/value-quality-composite-earnings-yield-roe-screen-914fad) | expr | top1000 | 0.00 | 3 | -28.44 |
| [Value-quality composite · top 3000 · monthly](https://data.finzdata.com/s/value-quality-composite-top-3000-monthly-b4dc9e) | expr | top3000 | 0.00 | 25 | -1.12 |
| [Value Quality Composite · top 500 · long only](https://data.finzdata.com/s/value-quality-composite-top-500-long-only-262e80) | expr | top500 | 0.00 | 18 | -2.43 |
| [Value · sales yield · top 50 of the Top 500 (after Barbee, Mukherji & Raines 1996) · long only](https://data.finzdata.com/s/value-sales-yield-top-50-of-the-top-500-after-barbee-mukherj-427cc1) | factor | top500 | 0.00 | 27 | -0.25 |
| [Value · sales yield · top 50 of the Top 500 (after Barbee, Mukherji & Raines 1996) · long/short](https://data.finzdata.com/s/value-sales-yield-top-50-of-the-top-500-after-barbee-mukherj-48be28) | factor | top500 | 0.00 | 27 | 3.97 |
| [Value x momentum blend · book-to-market + 126d skip 21 · top 50 · Top 500 · long only](https://data.finzdata.com/s/value-x-momentum-blend-book-to-market-126d-skip-21-top-50-to-91c54c) | expr | top500 | 0.00 | 19 | -3.39 |
| [Vol-Scaled Momentum 126d · Top 500 · Long Only](https://data.finzdata.com/s/vol-scaled-momentum-126d-top-500-long-only-9a88ed) | expr | top500 | 0.00 | 19 | -3.02 |
| [Volatility Adjusted Mean Reversion with RSI](https://data.finzdata.com/s/volatility-adjusted-mean-reversion-with-rsi-063d55) | expr | top500 | 0.00 | 20 | 0.87 |
| [Volatility Adjusted Momentum](https://data.finzdata.com/s/volatility-adjusted-momentum-684cac) | expr | top500 | 0.00 | 19 | -2.98 |
| [Volatility Adjusted Momentum · rank(ret126 / vol126)](https://data.finzdata.com/s/volatility-adjusted-momentum-rank-ret126-vol126-5d0b91) | expr | top1000 | 0.00 | 8 | -17.42 |
| [Volatility Adjusted Momentum Top 1000](https://data.finzdata.com/s/volatility-adjusted-momentum-top-1000-35cefa) | expr | top1000 | 0.00 | 25 | 1.31 |
| [Volatility-adjusted momentum with profitability](https://data.finzdata.com/s/volatility-adjusted-momentum-with-profitability-51987c) | expr | top3000 | 0.00 | 25 | -1.60 |
| [Volatility-scaled momentum · 126d return over 126d vol · top 50 · Top 500 · long only](https://data.finzdata.com/s/volatility-scaled-momentum-126d-return-over-126d-vol-top-50-e09442) | expr | top500 | 0.00 | 19 | -3.02 |
| [Volatility‑Weighted Momentum](https://data.finzdata.com/s/volatility-weighted-momentum-5080ec) | expr | top500 | 0.00 | 18 | -1.78 |
| [XLE 20/100 moving-average trend](https://data.finzdata.com/s/xle-20-100-moving-average-trend-8d8d05) | ma_cross | custom | 0.00 | 28 | -3.33 |
| [XLE 50/200 moving-average trend](https://data.finzdata.com/s/xle-50-200-moving-average-trend-cac4dc) | ma_cross | custom | 0.00 | 28 | -3.33 |
| [XLF 20/100 moving-average trend](https://data.finzdata.com/s/xlf-20-100-moving-average-trend-3c0690) | ma_cross | custom | 0.00 | 28 | -3.70 |
| [XLF 50/200 moving-average trend](https://data.finzdata.com/s/xlf-50-200-moving-average-trend-10666a) | ma_cross | custom | 0.00 | 28 | 0.14 |
| [XLK 20/100 moving-average trend](https://data.finzdata.com/s/xlk-20-100-moving-average-trend-71a0e9) | ma_cross | custom | 0.00 | 28 | -3.71 |
| [XLK 50/200 moving-average trend](https://data.finzdata.com/s/xlk-50-200-moving-average-trend-4daf6c) | ma_cross | custom | 0.00 | 28 | -4.81 |

## Contribute

Publish your own strategy through the MCP tool `publish_strategy` or `POST https://api.finzdata.com/v1/hub/publish` with a free API key; it appears here the next night. Every month's [challenge](https://data.finzdata.com/challenge) ranks entries on returns earned inside the month. Discuss strategies on their Hub pages.

## Data and license

Specs and results in this repository: CC BY 4.0, attribution "FinzData Hub". The underlying market data is served by the FinzData API (https://finzdata.com). Historical simulations for research and education; not investment advice. Past performance, simulated or real, does not predict future results.
