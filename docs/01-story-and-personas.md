# 01 — Story & Personas

## Firm Profile: Green Meadows
- **AUM:** $2B multi-strategy platform
- **Strategies:** Long/Short Equity, Credit, Macro, Multi-Strat
- **Counterparties:** 2 prime brokers, 2 fund administrators, 1 custodian
- **Internal systems:** OMS / EMS, accounting GL, CRM, internal research

## Primary persona — Sarah Chen, COO / CDO
- Accountable for **one source of truth** across PM, Risk, IR, and Ops.
- Sponsors data investments; reports data quality KPIs to the CIO.
- Today she fights:
  - Late, partial, or contradicting feeds from primes & admins.
  - Mapping drift (different security IDs, different sector taxonomies).
  - Manual recon spreadsheets blocking morning decisions.

## Secondary personas
| Persona | What they need from DataHub |
|---|---|
| **Portfolio Manager** | Trusted positions, P&L, exposures by 8am — refreshed intraday. |
| **Risk** | Aggregated VaR, factor exposures, scenario shocks across funds. |
| **Investor Relations** | Investor-ready NAV, performance, attribution, fact sheets. |
| **Ops** | Automated PB ↔ Admin reconciliation, exception workflow. |
| **Data Engineering** | Out-of-the-box ingestion + governed datasets so they can build, not plumb. |

## Success Criteria for the Demo
- Demonstrate **overnight automation**: ingest → normalize → reconcile → publish.
- Demonstrate **morning consumption**: dashboards, Ask DataHub, agents.
- Demonstrate **intraday streaming** alongside the governed foundation.
- Show **measurable lift**: 60%+ reduction in reconciliation effort, decisions hours earlier.
