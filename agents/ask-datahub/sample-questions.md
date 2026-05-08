# Ask DataHub — Sample Questions

Curated prompts for the **Ask DataHub** natural-language interface and downstream **AI agents**, grounded in the *Green Meadows* story (see [README](../../README.md)) and the actual Lakehouse schema (see [`fabric/ddl_export.sql`](../../fabric/ddl_export.sql)).

Use these for:
- Demo run-of-show
- Foundry agent evaluation datasets
- UAT / acceptance tests for new data feeds

---

## 1. NAV, AUM & PnL — CIO / IR view

- What's our total AUM as of yesterday, broken down by Portfolio Manager and Position Group?
- Show me YTD PnL by strategy, ranked best to worst.
- Which positions contributed the most to today's Daily PnL?
- What is the AUM-weighted YTD Sharpe ratio across the firm?
- Compare MTD PnL this month vs the same month last year.

## 2. Reconciliation — Ops view (the core pain point)

- For 2026-05-06, list securities where MSFS, JPMC, and FlexTrade quantities don't match.
- Which CUSIPs exist in JPMC custody but not in MSFS admin today?
- What's the total market-value break between Admin (MSFS) and Custody (JPMC) by Portfolio Manager?
- Show me the top 10 reconciliation breaks by absolute USD difference.
- Which positions are missing from the Bloomberg Security Master?

## 3. Exposure & Risk — Risk Officer view

- What's our current Net Delta exposure by asset class?
- Show top 10 positions by VaR95 contribution.
- Which positions have the highest CS01? Group by issuer.
- What is firm-wide VaR95 as a % of AUM, and how has it trended over the last 30 days?
- Show all positions with Vega-adjusted exposure greater than $5M.
- Which Portfolio Manager has the highest concentration risk (top 5 positions / total AUM)?

## 4. Trader / PM views

- Show me all my positions where price moved more than 5% today.
- What's my Daily PnL attribution by sector for PositionGroup `EquityLS`?
- List positions approaching their earnings date in the next 5 business days.
- Which of my long positions have the worst 30-day volatility?

## 5. Cross-source data quality

- How many securities are missing FIGIID enrichment in DataHub_Position?
- Show me the freshness of each source feed: latest Date in MSFS, JPMC, FlexTrade, Axioma.
- Which positions have a non-zero MSFS quantity but zero FlexTrade quantity?
- List securities where the financing rate from JPMC changed by more than 25 bps day-over-day.

## 6. Performance & attribution

- Compare 30-day vs 90-day Sharpe across all Portfolio Managers.
- What is the YTD volatility of our top 5 strategies, ranked?
- Decompose YTD PnL by AssetCategory and AssetSubCategory.

## 7. Agent-action prompts (automation, not just Q&A)

- Open a reconciliation ticket for every break > $100K and assign to the Ops on-call.
- Email today's risk pack (top 10 VaR contributors + Net Delta + CS01) to the IR distro.
- For tomorrow's earnings names in our book, draft an IR briefing note.
- Flag any position where %VaR95_AUM crossed the 2% threshold today and notify the PM on Teams.

---

## Coverage matrix (which tables each category hits)

| Category               | BB_SecurityMaster | MSFS | FlexTrade | JPMC | Axioma | DataHub_Position |
|------------------------|:-:|:-:|:-:|:-:|:-:|:-:|
| 1. NAV / AUM / PnL     |   | ✓ |   |   |   | ✓ |
| 2. Reconciliation      | ✓ | ✓ | ✓ | ✓ |   | ✓ |
| 3. Exposure / Risk     |   |   | ✓ |   | ✓ | ✓ |
| 4. Trader / PM         | ✓ |   | ✓ |   |   | ✓ |
| 5. Data quality        | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| 6. Performance / attr. |   |   |   |   |   | ✓ |
| 7. Agent actions       |   |   |   |   | ✓ | ✓ |
