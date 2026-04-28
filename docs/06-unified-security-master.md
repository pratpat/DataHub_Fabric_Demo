# 06 — Unified Security Master

A single golden record per security, used everywhere downstream.

## Inputs
- Refinitiv / FactSet / Bloomberg reference data.
- Provider security lists (each PB / admin uses different IDs).
- Internal additions (private placements, OTC instruments).

## Match keys (in order of precedence)
1. **ISIN**
2. **CUSIP**
3. **SEDOL**
4. **Bloomberg FIGI**
5. **Ticker + Exchange + Currency** (last resort)

## Survivorship rules
| Field | Source of truth |
|---|---|
| `name`, `issuer` | Refinitiv |
| `asset_class`, `instrument_type` | Internal taxonomy override → Refinitiv |
| `country_of_risk`, `country_of_listing` | FactSet |
| `gics_sector`, `gics_industry` | MSCI / GICS feed |
| `currency` | Provider with most recent update |
| `lifecycle_status` | Latest non-null across all sources |

## Output schema (gold)
```sql
CREATE TABLE gold.security_master (
  security_key    BIGINT      NOT NULL,  -- surrogate
  isin            STRING,
  cusip           STRING,
  sedol           STRING,
  figi            STRING,
  ticker          STRING,
  exchange        STRING,
  currency        STRING,
  name            STRING,
  issuer          STRING,
  asset_class     STRING,
  instrument_type STRING,
  gics_sector     STRING,
  gics_industry   STRING,
  country_listing STRING,
  country_risk    STRING,
  lifecycle_status STRING,
  effective_from  DATE,
  effective_to    DATE
) USING DELTA;
```

## Cross-references
A side table `gold.security_xref` maintains every external ID → `security_key` mapping for traceability.

## Demo angle
Show the **Security Master Browser** (Power BI page): pick AAPL, see every PB/admin's view collapsed into one record + the cross-ref tab listing every external code.
