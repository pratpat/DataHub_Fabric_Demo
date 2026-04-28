# 08 — Real-Time Streaming

The overnight gold layer is the **trusted foundation**. Intraday, real-time data is layered on top so traders, risk, and the CIO see live numbers without losing the governed reference set.

## Streams

| Stream | Source | Sink | Use |
|---|---|---|---|
| Market ticks / bars | Eventhub (Bloomberg / Refinitiv simulated) | KQL DB → `rt.market_ticks` | Live prices, marks |
| OMS executions | FIX → Eventstream | KQL DB → `rt.executions` | Intraday position drift, T-cost |
| Risk shocks | Risk engine webhook | KQL DB → `rt.risk_events` | Live VaR, scenario alerts |
| News / sentiment | Vendor webhook | KQL DB → `rt.news` | CIO alerts |

## Pattern
- **Eventstream** routes raw events to a **KQL Database**.
- **Materialized views** in KQL maintain real-time aggregates (intraday position by ticker, live exposure by sector).
- **Real-Time Dashboard** + **Power BI Direct Lake** show updates in seconds.
- A nightly job **promotes** intraday facts into gold for the next-day view.

## Trader / Risk views
- **Live blotter** — open orders, fills, slippage.
- **Live exposure heatmap** — net/gross by sector, geography.
- **Risk monitor** — VaR vs limits with breach alerts via Data Activator.

## Demo moment
- Show CIO dashboard at 9:00 AM (overnight gold).
- Trigger a simulated burst of trades + a market move.
- Watch the **same** dashboard refresh intraday with **live** numbers, while the audit/foundation remains the morning's gold snapshot.
