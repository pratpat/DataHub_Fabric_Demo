# 10 — AI Agents on DataHub

Specialist AI agents (Azure AI Foundry) automate workflows that previously required humans + spreadsheets.

| Agent | Purpose | Tools / Grounding |
|---|---|---|
| **Reconciliation Agent** | Triage `recon_breaks`, propose root cause, auto-resolve known patterns | Ask DataHub (Fabric Data Agent), rule library |
| **NAV Agent** | Validate end-of-day NAV vs internal calc, draft variance memo | Fabric Data Agent + email tool |
| **Investor Relations Agent** | Generate fund fact sheets, answer LP questions, draft monthly letter | Fabric Data Agent + PowerPoint / Word tools |
| **Ops Co-Pilot** | Surface breaks of the day, assign owners, send Teams nudges | Data Activator + Graph (Teams + Outlook) |
| **Risk Watchdog** | Monitor live VaR & limit breaches, escalate to CIO with context | KQL real-time views + Bing grounding for news context |

## Pattern (per agent)
- Single-purpose Foundry agent with clear instructions.
- Fabric Data Agent (`Ask DataHub`) attached as a **tool** for grounded answers.
- Optional tools: Microsoft Graph (Teams / Outlook / SharePoint), PowerPoint generation, Bing.
- Orchestrated either standalone or via a **Copilot Studio** multi-agent surface published to Teams.

## Repo layout

```
agents/
├── ask-datahub/                 # Fabric Data Agent definition
└── ai-foundry/
    ├── reconciliation-agent/
    ├── nav-agent/
    ├── investor-relations-agent/
    ├── ops-copilot/
    └── risk-watchdog/
```

Each folder contains `agent.yaml`, `instructions.md`, and `tools/`.

## Example: Reconciliation Agent prompt

```
You are the Reconciliation Agent for Green Meadows. Your job is to triage rows in
gold.recon_history.status='OPEN' from the previous business day.

For each break:
1. Use Ask DataHub to fetch related positions, transactions, and corporate actions.
2. Match against the rule library (corp_action_timing, fx_source_diff, settlement_lag).
3. If a pattern matches with > 90% confidence, propose AUTO-RESOLVE with a justification.
4. Otherwise, classify the likely root cause and assign to the named ops owner.
5. Post a daily summary to the #ops-recon Teams channel.

Never modify positions or transactions directly. Only update recon_breaks.status and
recon_breaks.resolution_note.
```
