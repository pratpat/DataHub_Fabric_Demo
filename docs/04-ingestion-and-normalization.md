# 04 — Ingestion & Normalization

## Bronze — raw, immutable
- One folder per source under `Files/bronze/<source>/<yyyy>/<mm>/<dd>/`.
- File-arrival triggers (Data Factory + Event Grid) kick off ingestion.
- No transformations beyond schema-on-read. Original payload preserved for audit.

## Silver — canonical schema
DataHub publishes a **canonical model** that all source data is mapped into:

| Canonical entity | Description |
|---|---|
| `position` | Quantity + cost per (fund, account, security, as_of) |
| `cash_balance` | Balance per (fund, account, currency, as_of) |
| `transaction` | Buy/sell/corp-action per (fund, account, security, txn_date) |
| `nav` | Daily NAV per (fund, share_class, as_of) |
| `pnl` | Daily P&L per (fund, account, security) |
| `margin` | Margin & financing per (fund, account, broker, as_of) |
| `security_master` | Single record per security (see doc 06) |
| `fx_rate` | Per currency pair, per as_of |

## Mapping
- **Provider → canonical** mappings live in YAML under `fabric/mappings/`:
  ```yaml
  provider: PrimeBroker_A
  entity: position
  fields:
    fund_code:        BookID
    account:          Account
    security_id:      CUSIP
    security_id_type: CUSIP
    quantity:         Qty
    cost:             AvgCost
    as_of:            AsOfDate
  ```
- Mappings are versioned in git → reproducible silver builds.

## Normalization steps (per source, per day)
1. **Load bronze** → DataFrame.
2. **Apply mapping** → canonical columns.
3. **Resolve security IDs** against Security Master → canonical `security_key`.
4. **FX-normalize** monetary fields to USD using daily `fx_rate`.
5. **Sign conventions** — enforce house convention (positions long > 0, short < 0).
6. **Validate** — non-null keys, allowed enums, balance checks.
7. **Write Silver Delta** with provider tag retained for lineage.

Notebook entry point: [`fabric/notebooks/10_normalize_to_silver.py`](../fabric/notebooks/10_normalize_to_silver.py)
