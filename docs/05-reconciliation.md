# 05 — Reconciliation

DataHub automates the three-way recon that ops teams typically do by hand.

## The three-way

```
   Prime Broker  ──┐
                   ├──▶  DataHub Reconciliation Engine  ──▶  Exceptions Workbench
   Fund Admin   ──┤                                          (Power BI + Data Activator)
                   │
   OMS / Internal ┘
```

## Recon types delivered out-of-the-box

| Recon | Compares | Tolerance |
|---|---|---|
| **Position** | PB qty vs Admin qty vs Internal qty per (fund, security, as_of) | 0 shares (configurable) |
| **Cash** | PB cash vs Admin cash per (fund, ccy, as_of) | $0.01 |
| **Transaction** | PB trade vs OMS execution per (txn_id / ticker / date / side / qty) | 0 shares |
| **NAV** | Admin NAV vs internal calculated NAV per (fund, as_of) | 1 bp |
| **Corporate Actions** | PB CA application vs Admin CA application | event-level match |
| **FX** | Provider FX vs reference rate | 5 bps |

## Exception workflow
1. Engine writes breaks to `silver.recon_breaks` Delta table.
2. **Data Activator** rule: when new break appears, post to a Teams channel + open a card in the Ops Workbench.
3. Ops user assigns owner, comments, and marks resolved (or auto-resolved by an AI agent — see [10-ai-agents.md](10-ai-agents.md)).
4. Resolved breaks are appended to `gold.recon_history` for trend KPIs.

## KPIs
- **Break rate** (% of positions/cash with a break).
- **Mean time to resolve.**
- **% resolved by automation vs human.**
- **Recurring root-cause categories** (e.g., corp action timing, FX source).
