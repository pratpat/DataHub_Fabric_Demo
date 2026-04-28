# 11 — Demo Script

> **Setting:** Monday, 8:30 AM. Sarah Chen opens her CIO dashboard before the trading day.

## Act 1 — The "before" (90 sec)
- Show 5 tabs: PB-A statement (PDF), PB-B (CSV), Geneva (XML), market data (CSV), an Ops recon spreadsheet with red cells.
- **Punchline:** *"It's Tuesday before they trust Monday's numbers."*

## Act 2 — Overnight DataHub run (3 min)
- Open the Fabric workspace `GreenMeadows-DataHub`.
- Show the **Data Factory pipeline** that ran overnight: ingest → normalize → reconcile → publish.
- Drill into:
  - **Bronze**: raw landing (immutable).
  - **Silver**: canonical schema with provider tag.
  - **Security Master**: pick AAPL → show survivorship + cross-refs from each PB/admin.
  - **Recon dashboard**: 4 breaks, 3 auto-classified.

## Act 3 — Monday-morning consumption (4 min)
- Open the **CIO dashboard** in Power BI:
  - Firm-wide NAV, gross/net exposure, sector heatmap.
  - As-of stamp = today 06:00 ET.
- Switch to the **PM page** — strategy P&L, top movers.
- Switch to the **Risk page** — VaR, factor exposures.
- Switch to the **IR page** — fund fact sheet preview.
- Same numbers everywhere — *one source of truth*.

## Act 4 — Ask DataHub (3 min)
- In Teams, open **Ask DataHub**. Ask live:
  1. *"What's our firm-wide net exposure to regional banks as of this morning?"*
  2. *"Show today's top 5 P&L contributors and the news driving them."*  ← demonstrates real-time + grounding.
  3. *"Which positions broke between Goldman and Geneva this week?"*

## Act 5 — AI agents take work off the team (3 min)
- **Reconciliation Agent** posts in `#ops-recon`: 3 of 4 breaks auto-resolved with justification + audit link; 1 assigned to Maria.
- **IR Agent** generates the **April fund fact sheet** (.pptx) ready for Sarah to review and send.
- **Risk Watchdog** flags a live VaR breach with a Bing-grounded news context (e.g., bond market move).

## Act 6 — Intraday streaming (2 min)
- Trigger simulated trade & market burst.
- Watch the **CIO dashboard** Direct Lake page update intraday — live numbers next to the morning's gold snapshot.

## Closing message
> *Sarah's team didn't change their primes, admins, or systems. DataHub on Fabric ingests, normalizes, reconciles, and governs all of it overnight — and exposes it through dashboards, Ask DataHub, and AI agents. **Decisions hours earlier. 60% less reconciliation. One trusted view across PM, Risk, IR, and Ops.***

## Run-of-show (≈ 16 min)

| Min | Segment |
|---|---|
| 0–2 | Persona + the pain |
| 2–5 | Overnight DataHub run |
| 5–9 | Monday-morning consumption |
| 9–12 | Ask DataHub |
| 12–14 | AI agents |
| 14–16 | Intraday streaming + close |
