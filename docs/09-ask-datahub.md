# 09 — Ask DataHub

**Ask DataHub** is the natural-language entry point into the governed Fabric estate. It is implemented as a **Fabric Data Agent** over the gold semantic model, with curated prompts and guardrails.

## What users can ask

- *"What is our net exposure to regional banks across all funds as of yesterday?"*
- *"Show today's top 10 P&L contributors and detractors firm-wide."*
- *"Which positions broke between Goldman and Geneva this week?"*
- *"Performance attribution by strategy month-to-date."*
- *"How much cash is unencumbered across our prime brokers right now?"*

## How it works
1. User asks in Teams / Power BI / web.
2. Ask DataHub (Fabric Data Agent) translates to semantic-model queries.
3. Results return as tables + auto-generated visuals + a written narrative.
4. Citations link back to the **gold** tables and freshness timestamps.

## Setup steps
1. In the `Green Meadows` Fabric workspace → **+ New → Data Agent** → name **`Ask DataHub`**.
2. Add data source = the **gold Warehouse** + its **Semantic Model**.
3. Paste the system prompt below.
4. Add the example queries above as few-shot.
5. Publish to Teams / Power BI / M365 Copilot.

## System prompt

```
You are Ask DataHub, the natural-language interface for Green Meadows, a $2B
multi-strategy fund. You answer questions about positions, exposures, NAV, P&L,
cash, and reconciliation breaks using the certified gold Warehouse and its
Semantic Model.

RULES:
- Always use semantic-model measures (Market Value, Gross/Net Exposure, NAV, P&L).
- Always disclose the as-of timestamp and source layer (gold / real-time).
- Never expose investor PII.
- If a question requires real-time intraday data, pull from the KQL real-time
  views and label results "intraday".
- For reconciliation questions, query gold.recon_history.
- Decline to give investment advice; provide informational answers only.
```
