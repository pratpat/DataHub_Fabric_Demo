# 07 — Governed Datasets in Fabric

DataHub publishes **gold** datasets as Fabric **Warehouse tables** + a **Semantic Model** so every consumer sees the same numbers.

## Star schema (gold)

```
                 dim_security
                      │
dim_fund ── fact_positions ── dim_date
                      │
                 dim_account
```

| Table | Type | Grain |
|---|---|---|
| `dim_fund` | dim | one row per fund / share class |
| `dim_account` | dim | one row per account at a custodian/PB |
| `dim_security` | dim | golden security master |
| `dim_date` | dim | calendar |
| `fact_positions` | fact | (fund, account, security, as_of) |
| `fact_transactions` | fact | (fund, account, security, txn_id) |
| `fact_cash` | fact | (fund, account, ccy, as_of) |
| `fact_nav` | fact | (fund, share_class, as_of) |
| `fact_pnl` | fact | (fund, account, security, as_of) |

## Semantic Model — measures
| Measure | Definition |
|---|---|
| `Market Value (USD)` | Σ qty × price × fx |
| `Gross Exposure` | Σ |Market Value| |
| `Net Exposure` | Σ Market Value (signed) |
| `NAV` | Latest fact_nav |
| `Daily P&L` | fact_pnl day-over-day |
| `MTD / YTD P&L` | DATESMTD / DATESYTD over Daily P&L |
| `Sector Exposure %` | Market Value by GICS sector / Gross Exposure |
| `VaR (1-day, 99%)` | from risk feed (gold.fact_risk) |

## Power BI deliverables (out of the box)
- **CIO dashboard** — NAV, exposures, top movers
- **PM page** — by strategy, sector, geography
- **Risk page** — VaR, factor exposures, scenarios
- **IR page** — investor-ready performance & attribution
- **Ops page** — recon breaks, DQ KPIs

## Governance
- All gold items endorsed (**Certified**) in Fabric.
- Row-level security: PMs see their book; IR sees fund-level only.
- Lineage published to Purview.
