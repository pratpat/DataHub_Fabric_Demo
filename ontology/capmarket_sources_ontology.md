# CapMarket DataHub - Source Ontology

Derived from the Lakehouse DDL in [`fabric/ddl_export.sql`](../fabric/ddl_export.sql).
`DataHub_Position` (the mastered/golden table) is intentionally omitted to focus on source-system entities.

```mermaid
erDiagram
    BB_SecurityMaster ||--o{ MSFS_Position      : "identifies (CUSIP/ISIN/SEDOL/Ticker)"
    BB_SecurityMaster ||--o{ FlexTrade_Position : "identifies"
    BB_SecurityMaster ||--o{ JPMC_Position      : "identifies"
    BB_SecurityMaster ||--o{ Axioma_Risk        : "identifies"

    MSFS_Position      ||--o{ Axioma_Risk : "risk-scored (same security/date)"
    FlexTrade_Position ||--o{ Axioma_Risk : "risk-scored"
    JPMC_Position      ||--o{ Axioma_Risk : "risk-scored"

    BB_SecurityMaster {
        string Ticker
        string BloombergTicker
        string CUSIP
        string ISIN
        string SEDOL
        string FIGIID
        string ID_BB_GLOBAL
        string AssetType
        string AssetTypeBBG
        string EarningsDate
    }

    MSFS_Position {
        string Date
        string Ticker
        string CUSIP
        string ISIN
        string SEDOL
        string PBSource
        string AssetCategory
        string AssetSubCategory
        double QuantityEOD
        double MarketValueBaseEOD
        double DailyPNL
        double MTDPNL
        double YTDPNL
        double AUM
        double UnitCost
    }

    FlexTrade_Position {
        string Date
        string Ticker
        string CUSIP
        string ISIN
        string SEDOL
        double QuantityEOD
        double MarketValueBaseEOD
        double Delta
        double DeltaAdjustedNetExposure
        double TickUnit
        double CS01
        double RiskFactor
        double Vega
        double VegaAdjustedExposure
    }

    JPMC_Position {
        string Date
        string Ticker
        string CUSIP
        string ISIN
        string SEDOL
        double FinancingRate
        double QuantityEOD
        double MarketValueBaseEOD
    }

    Axioma_Risk {
        string Date
        string Ticker
        string CUSIP
        string ISIN
        string SEDOL
        double VaR90
        double VaR95
        double VaR99
        double Pct_VaR95
        double Pct_VaR95_AUM
        double Pct_VaR90
        double Pct_VaR90_AUM
        double Pct_VaR99
        double Pct_VaR99_AUM
        string VaRPeriod
    }
```

## Notes

- **Reference layer:** `BB_SecurityMaster` carries the canonical Bloomberg identifiers (`FIGIID`, `ID_BB_GLOBAL`).
- **Common join key:** `Date` + (`CUSIP` | `ISIN` | `SEDOL` | `Ticker`) + `PortfolioManager` + `PositionGroup`.
- **Source specialization:**
  - `MSFS_Position` (admin) - PnL (`DailyPNL/MTDPNL/YTDPNL`), `AUM`, `UnitCost`, classification
  - `FlexTrade_Position` (EMS) - Greeks (`Delta`, `Vega`), risk factors, exposures
  - `JPMC_Position` (custody) - `FinancingRate`
  - `Axioma_Risk` - VaR suite (`VaR90/95/99`, `Pct_VaR*`)
