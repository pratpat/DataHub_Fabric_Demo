# 03 — Data Sources

A realistic mix of **batch** and **streaming** sources for a $2B multi-strategy fund.

## Batch (overnight)

| # | Source | Format | Cadence | Sample dataset |
|---|---|---|---|---|
| 1 | **Prime Broker A** (e.g., Goldman Sachs) | Fixed-width / PDF | EOD | Positions, cash, margin |
| 2 | **Prime Broker B** (e.g., Morgan Stanley) | CSV via SFTP | EOD | Trades, settlements |
| 3 | **Fund Administrator A** (e.g., SS&C Geneva) | XML / Excel | T+1 | NAV pack, GL trial balance |
| 4 | **Fund Administrator B** (e.g., Citco) | CSV | T+1 | NAV, fees, accruals |
| 5 | **Custodian** (e.g., BNY / State Street) | SWIFT MT535 / MT940 | EOD | Holdings, cash balances |
| 6 | **Internal Accounting** | DB extract | EOD | GL postings |
| 7 | **Security Master Source** | Refinitiv / FactSet API | Daily | Reference, classifications |

## Streaming (intraday)

| # | Source | Transport | Demo dataset |
|---|---|---|---|
| 8 | **Market Data** | Eventhub / Kafka (Bloomberg B-PIPE simulated) | Tick / minute bars |
| 9 | **OMS / EMS** | FIX log → Eventstream | Orders, executions |
| 10 | **News / Alt** | Webhook | Headlines, sentiment, ESG events |

## Demo data approach

- **Start small but believable:** 3 funds × ~150 positions × 5 business days × 2 PBs + 1 admin + 1 custodian.
- **Plant deliberate frictions** so reconciliation has something to find:
  - 1 corporate action mishandled by PB-A (quantity off by split factor).
  - 1 missing CUSIP on a new issue.
  - 1 timing difference (T+0 PB vs T+1 admin) on a settled trade.
  - 1 FX rate divergence between providers.
- **Generators** under [`fabric/notebooks/`](../fabric/notebooks/) produce CSV/JSON/XML extracts deterministically (seeded RNG).
