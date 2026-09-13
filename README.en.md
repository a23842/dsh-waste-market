# DSH Waste Market

An A-share market dashboard plugin for [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness): a draggable trading panel inside the DSH Web UI.

Click the 📊 entry in the DSH sidebar to open the dashboard:

| Tab | What it does |
| --- | --- |
| Indices | SSE / SZSE / ChiNext / STAR50 realtime indices, 30s auto-refresh |
| Quotes | Stock / ETF / convertible bond / LOF search (code / name / pinyin) + multi-pool rankings (A-shares / ETFs / convertibles / LOF, sorted by change / turnover / volume ratio / main net inflow) + sector board + Dragon-Tiger list |
| Watchlist | Realtime quotes with persistence at `$DSH_HOME/.waste-market-watchlist.json`; groups, JSON backup / CSV export, import from JSON / CSV / text (merge or replace) |
| Screener | Two modes: **score screener** (price / change / turnover filters + 13 technical signal toggles + weighted scoring) and **multi-strategy crossover screener** (7 preset strategies in parallel; a stock must hit several at once, sorted by hit count) |
| News | 7×24 flash news aggregated from Sina / Eastmoney / Jin10; filterable by source, important items highlighted, 60s auto-refresh |

A **market sentiment thermometer** sits at the top: limit-up / limit-down / broken-board counts, market-wide advance/decline breadth, and the consecutive-board ladder with an expandable limit-up pool.

Clicking any stock opens a detached detail window: large price + change, 15 realtime metrics, daily / weekly / monthly / 5m / 30m K-line (forward-adjusted, candlesticks + MA5/10/20 + volume + crosshair). Windows are draggable, multiple can be open, ESC or overlay click closes them.

## Data sources

- Realtime quotes / indices / K-line: Tencent Finance (qt.gtimg.cn / ifzq.gtimg.cn, multi-host fallback)
- Rankings / sectors / Dragon-Tiger / fund flow / screener universe: Eastmoney (push2.eastmoney.com, multi-mirror fallback)
- Search: Eastmoney (searchadapter.eastmoney.com)
- 7×24 flash news: Sina Finance live feed + Eastmoney flash + Jin10

All data is proxied by the plugin's server half and served to the browser same-origin under `/api/waste-market/*`, loopback only. Research use only — not investment advice.

## Install

```bash
dsh plugin --profile web add @czf1995/dsh-waste-market
```

Or manually (development mode):

```bash
# 1. Link the package into the web profile's node_modules (or pnpm add file:...)
ln -s "<repo path>" "$DSH_HOME/profiles/web/node_modules/@czf1995/dsh-waste-market"

# 2. Append to $DSH_HOME/profiles/web/cordis.patch.yml:
# - insert:
#     - id: waste-market
#       name: '@czf1995/dsh-waste-market'

# 3. The profile watcher recomposes in ~1s; hard-refresh the page to see the 📊 entry.
#    If lib/index.js (server half) changed, restart the DeepSeek Harness process.
```

## Structure

- `lib/index.js` — server cordis plugin: `/api/waste-market/*` routes + watchlist persistence + news aggregation
- `lib/emrank.js` — Eastmoney rankings / sectors / Dragon-Tiger / fund flow
- `lib/fetch-utils.js` — shared fetch helpers (GBK decode, multi-host fallback)
- `lib/screener.js` — screener engine
- `lib/client.js` — browser panel (React)
- `cordis.patch.yml` — plugin row registration (`dsh.bundle.patch`)

## License

MIT