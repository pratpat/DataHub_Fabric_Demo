# DataHub on Microsoft Fabric — Green Meadows Demo

> **Solution:** *Financial Fabric — DataHub for Capital Markets* deployed on Microsoft Fabric.
> **Persona:** **Sarah Chen**, COO / CDO of **Green Meadows**, a $2B multi-strategy platform running multiple prime brokers, fund administrators, and internal systems.
> **Goal:** Show how DataHub turns Monday-morning data chaos into a governed, trusted foundation that powers PMs, Risk, IR, Ops — and AI agents — from the same data.

---

## The Story

### The Firm
**Green Meadows** — $2B multi-strategy platform. Multiple prime brokers, multiple fund administrators, multiple internal systems (OMS, EMS, accounting, CRM).

### The Persona
**Sarah Chen — COO / CDO.** Accountable for ensuring PM, Risk, Investor Relations, and Operations are aligned off the **same** data.

- Her **ops team** spends > **60%** of its time reconciling data instead of analyzing it.
- Her **data team** spends most of its time on ingestion, normalization, and cleanup — not building analytics or tools.

### The Pain (Monday morning)
The firm needs a consolidated view of **NAV, exposures, and risk** across funds and strategies. Today:

- Data arrives from primes, admins, and internal systems on **different schedules**.
- Definitions and mappings **don't fully align** across providers.
- Positions, cash, and transactions still require **manual reconciliation**.

So instead of starting the week with **decision-making**, the firm spends hours **aligning and validating** data before it can be used — and is always behind the ball.

### The DataHub Solution

Overnight, **DataHub** ingests, normalizes, and reconciles data across all sources, constructs a **unified security master**, and publishes **governed datasets** into Microsoft Fabric.

By Monday morning, Green Meadows isn't bogged down assembling and reconciling data — it's operating on a **consistent, trusted foundation** across all teams.

As the trading day progresses, **real-time data** is streamed alongside the overnight foundation and delivered into:
- Trader views
- Risk monitoring
- CIO / IR dashboards

On top of this, **Ask DataHub** provides a direct natural-language interface — letting users query the data, generate insights, and deploy **AI agents** to automate workflows across the firm.

---

## Solution at a Glance

```mermaid
flowchart LR
    subgraph Sources["Source Systems"]
      PB1[Prime Broker A<br/>positions, cash, margin]
      PB2[Prime Broker B<br/>positions, cash, margin]
      ADM1[Fund Admin A<br/>NAV, GL]
      ADM2[Fund Admin B<br/>NAV, GL]
      OMS[OMS / EMS<br/>orders, executions]
      ACC[Internal Accounting]
      MKT[Market Data<br/>prices, FX, ref data]
      ALT[Alt Data<br/>ESG, news, factors]
    end

    subgraph DH["DataHub on Fabric"]
      ING[Ingestion<br/>APIs / SFTP / Kafka]
      NORM[Normalization<br/>+ Mapping]
      REC[Reconciliation<br/>PB ↔ Admin ↔ OMS]
      SM[Unified<br/>Security Master]
      LH[(Governed<br/>Lakehouse / Warehouse)]
      SM_MDL[Semantic Model<br/>Ontology]
    end

    subgraph Consumers["Consumers"]
      TRADE[Trader Views]
      RISK[Risk Monitoring]
      CIO[CIO / IR Dashboards]
      ASK[Ask DataHub<br/>NL Q&A]
      AGENTS[AI Agents<br/>Reconciliation / IR / Ops]
    end

    PB1 & PB2 & ADM1 & ADM2 & OMS & ACC & MKT & ALT --> ING
    ING --> NORM --> REC --> SM --> LH --> SM_MDL
    SM_MDL --> TRADE & RISK & CIO & ASK & AGENTS
```

---

## Source Ontology

Derived from the actual Lakehouse DDL ([`fabric/ddl_export.sql`](fabric/ddl_export.sql)). `BB_SecurityMaster` is the canonical reference; the position sources (MSFS / FlexTrade / JPMC) are joined with `Axioma_Risk` on `Date` + security identifiers.

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

> Full attribute-level diagram and notes: [`ontology/capmarket_sources_ontology.md`](ontology/capmarket_sources_ontology.md)

---

## Repo Structure

```
DataHub_Fabric_Demo/
├── README.md
├── docs/
│   ├── 01-story-and-personas.md
│   ├── 02-architecture.md
│   ├── 03-data-sources.md            # Primes, admins, OMS, market, alt
│   ├── 04-ingestion-and-normalization.md
│   ├── 05-reconciliation.md          # PB ↔ Admin ↔ OMS breaks
│   ├── 06-unified-security-master.md
│   ├── 07-governed-datasets-fabric.md
│   ├── 08-realtime-streaming.md
│   ├── 09-ask-datahub.md             # NL Q&A over Fabric
│   ├── 10-ai-agents.md               # Foundry agents, automation
│   └── 11-demo-script.md             # Run-of-show
├── data/
│   ├── schemas/                      # DDL for canonical model
│   └── sample/                       # Synthetic source extracts
├── fabric/
│   ├── lakehouses/                   # Bronze / Silver / Gold layouts
│   ├── notebooks/                    # PySpark for ingest, normalize, reconcile
│   └── pipelines/                    # Data Factory pipelines
├── ontology/
│   └── datahub.ontology.json
├── agents/
│   ├── ask-datahub/                  # NL interface config
│   └── ai-foundry/                   # Reconciliation / IR / Ops agents
└── .gitignore
```

---

## Quick Start

1. **Story & personas** — [docs/01-story-and-personas.md](docs/01-story-and-personas.md)
2. **Architecture** — [docs/02-architecture.md](docs/02-architecture.md)
3. **Data sources** — [docs/03-data-sources.md](docs/03-data-sources.md)
4. **Ingestion & normalization** — [docs/04-ingestion-and-normalization.md](docs/04-ingestion-and-normalization.md)
5. **Reconciliation** — [docs/05-reconciliation.md](docs/05-reconciliation.md)
6. **Unified Security Master** — [docs/06-unified-security-master.md](docs/06-unified-security-master.md)
7. **Governed datasets in Fabric** — [docs/07-governed-datasets-fabric.md](docs/07-governed-datasets-fabric.md)
8. **Real-time streaming** — [docs/08-realtime-streaming.md](docs/08-realtime-streaming.md)
9. **Ask DataHub** — [docs/09-ask-datahub.md](docs/09-ask-datahub.md)
10. **AI Agents** — [docs/10-ai-agents.md](docs/10-ai-agents.md)
11. **Demo script** — [docs/11-demo-script.md](docs/11-demo-script.md)
