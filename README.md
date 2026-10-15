# Massive.com Postman Collection

Ready-to-run Postman requests for the Massive.com (formerly Polygon.io) REST API. It covers
every documented REST endpoint (127 unique paths) across stocks, options, futures, indices,
forex, crypto, economy, and partner data.

## Files

| File | What it is |
| --- | --- |
| `Massive.postman_collection.json` | 146 GET requests in folders by asset class |
| `Massive.postman_environment.json` | `baseUrl`, your API key, and sample tickers |

## Setup

1. Postman → **Import** → drop in both JSON files.
2. Top-right environment picker → select **Massive**.
3. Open the environment and paste your key into `MASSIVE_API_KEY` (get one at
   <https://massive.com/dashboard/keys>). It is a `secret` variable, so put it in the
   **Current value** column. That value stays local and is not synced or exported.
4. Run **0. Smoke test (free plan)** → *Previous close (stock)*. A `200` with
   `"status": "OK"` means everything is wired up.

Auth is set once on the collection as `Authorization: Bearer {{MASSIVE_API_KEY}}`. No
request puts the key in the URL.

## Folders

| Folder | Contents |
| --- | --- |
| 0. Smoke test | Market status, holidays, previous close |
| 1. Stocks | Bars, open/close, grouped daily, snapshots, movers, unified snapshot, trades, quotes |
| 2. Stocks: corporate actions & fundamentals | Dividends, splits, IPOs, ticker events, float, short interest/volume, financial statements, ratios |
| 3. Stocks: SEC filings | EDGAR index, 10-K sections, 8-K, Forms 3/4, 13-F, risk factors, taxonomies |
| 4. Indices | Snapshot, bars, previous close, open/close, ticker search/details |
| 5. Options | Contracts, chain and contract snapshots, bars, open/close, trades, quotes, exchanges, conditions |
| 6. Futures | Contracts, products, schedules, market status, exchanges, bars, snapshot, trades, quotes |
| 7. Crypto | Bars, open/close, grouped, snapshots, movers, last trade, trades, reference |
| 8. Forex | Bars, grouped, conversion, last quote, quotes, snapshots, movers, reference |
| 9. Technical indicators | SMA, EMA, RSI, MACD for each of stocks, indices, options, crypto, forex |
| 10. Reference | Tickers, types, related companies, news, exchanges, conditions, deprecated v3 dividends/splits |
| 11. Economy | Treasury yields, inflation, inflation expectations, labor market, funding conditions |
| 12. Alternative & partner data | Benzinga, ETF Global, TMX, European consumer spending (paid add-ons) |
| 13. Pagination | Follow the last saved `next_url` |

## Variables

| Variable | Default | Notes |
| --- | --- | --- |
| `stockTicker` | `AAPL` | Bare symbol |
| `watchlist` | `AAPL,MSFT,NVDA,SPY` | Comma-separated |
| `indexTicker` | `I:SPX` | `I:` prefix |
| `optionTicker` | `O:AAPL261218C00200000` | OCC symbol. If it 404s, run *Options → Contracts for underlying* and paste a live one |
| `futuresProduct` | `ES` | Product code, used as a filter |
| `futuresTicker` | `ESZ6` | Contract ticker. Contracts expire: after a roll, run *Futures → Contracts for product* and paste the first row's `ticker` |
| `etfTicker` | `SPY` | ETF Global `composite_ticker` |
| `cryptoTicker` | `X:BTCUSD` | REST format (WebSocket uses `BTC-USD`) |
| `fxTicker` | `C:EURUSD` | REST format (WebSocket uses `EUR/USD`) |
| `from` / `to` / `date` | empty | Left empty, a pre-request script fills them: `to`/`date` = last weekday, `from` = 30 days earlier. Set them (`YYYY-MM-DD`) to override |

## Reading the Results

Every request runs a test script. Check the **Test Results** tab and the Postman console:

| Response | Meaning |
| --- | --- |
| `200`, `status: OK` | Good. `OK` does **not** mean real-time |
| `200`, `status: DELAYED` | Your plan gets delayed data for this endpoint |
| `400` | A parameter is invalid |
| `401` | Key missing or wrong: check the environment is selected |
| `403 NOT_AUTHORIZED` | Your plan does not include this endpoint. The key is fine |
| `404` | Unknown ticker, or an expired option/futures contract |
| `429` | Rate limited. The free plan allows about 5 calls/minute |

Each request's description lists the minimum plan. Each asset class has its own plan family
(Stocks, Options, Indices, Futures, Currencies), so "Basic" means that class's Basic tier. On
Basic, these work: folder 0, all of 3, 10, and 11, folder 2 except the financial statements
and ratios (Advanced), and the bars, open/close, grouped, indicator, and reference requests in
the class folders. Snapshots, crypto trades, and forex conversion and quotes need Starter.
Stock, option, and futures trades need Developer. Stock and option quotes need Advanced
(futures quotes: Developer). Folder 12 needs per-dataset add-ons.

## Pagination

When a response has `next_url`, the test script saves it. Run **13. Pagination → Follow
next_url** to fetch the next page. The Bearer header goes with it automatically, because
cursor URLs are not pre-authenticated.

## Timestamps

- Bars `t`, indicators, and WebSocket: **ms** (13 digits)
- Snapshots `updated`, v3 trades/quotes, and all futures data: **ns** (19 digits)

## Not Included

WebSocket channels and Flat Files (S3) are not REST, so Postman cannot run them here. See
the skill references for those.

Full endpoint list: `D:\Techspace\massive\skills\massive\references\endpoint-index.md`.
