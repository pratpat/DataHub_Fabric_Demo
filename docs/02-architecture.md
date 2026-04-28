# 02 — Architecture

## Layered View (Bronze / Silver / Gold on Fabric)

```mermaid
flowchart TB
    subgraph Sources["Source Systems"]
      PB[Prime Brokers]
      AD[Fund Admins]
      OMS[OMS / EMS]
      ACC[Accounting]
      MKT[Market & Ref Data]
      ALT[Alt Data]
    end

    subgraph Ingest["DataHub Ingestion (Fabric Data Factory + Eventstream)"]
      BATCH[Batch / SFTP / API]
      STREAM[Streaming / Kafka / Eventhub]
    end

    subgraph Bronze["Bronze (Raw, immutable)"]
      B1[(Lakehouse: bronze)]
    end

    subgraph Silver["Silver (Normalized, mapped)"]
      S1[(Lakehouse: silver)]
      SM[Security Master]
      MAP[Provider Mappings]
    end

    subgraph Gold["Gold (Governed, business-ready)"]
      G1[(Warehouse: gold)]
      DM[Dimensional Model]
      ONT[Semantic Ontology]
    end

    subgraph Consume["Consumption"]
      PBI[Power BI Dashboards]
      ASK[Ask DataHub]
      AGT[AI Agents (Foundry)]
      API[Data API / Notebooks]
    end

    PB & AD & OMS & ACC --> BATCH
    MKT & ALT --> STREAM
    BATCH --> B1
    STREAM --> B1
    B1 --> S1
    SM --- S1
    MAP --- S1
    S1 --> G1
    G1 --- DM
    G1 --- ONT
    G1 --> PBI & ASK & AGT & API
```

## Component map

| Layer | Fabric component | Purpose |
|---|---|---|
| Ingest (batch) | **Data Factory pipelines** | Pull from PB / Admin SFTP, REST APIs |
| Ingest (stream) | **Eventstream** | Real-time market data, OMS executions |
| Bronze | **Lakehouse** (raw Delta) | Immutable raw landing, partitioned by source/date |
| Silver | **Lakehouse** + **PySpark notebooks** | Normalize to canonical schema, apply security master |
| Reconciliation | **Notebooks + Data Activator** | Cross-source breaks, exception alerts |
| Gold | **Warehouse** + **Semantic Model** | Governed star/snowflake schema, ontology |
| Real-time | **KQL Database** + **Real-Time Dashboard** | Intraday positions, exposures |
| Consumption | **Power BI**, **Ask DataHub**, **Foundry agents** | Dashboards, NL Q&A, automation |

## Data Quality & Governance
- **Lineage:** Microsoft Purview integration over Fabric items.
- **Quality rules:** Notebook-driven DQ checks per silver table; Data Activator alerts.
- **Access:** OneLake security + workspace RBAC; row-level security per fund.
- **Audit:** Bronze is immutable; all transforms reproducible from Silver downward.
