---
name: kpi-definitions
title: KPI Definitions & Measurement Basis — Facilitated Session
type: Skill
description: Build or update a BESS project's KPI Definition & Measurement Basis document through a facilitated session with the asset manager. Use whenever the user wants to define availability/capacity/SOH/RTE/dispatch-compliance KPIs, resolve disagreements between systems reporting different numbers for the same metric (APM vs LTSA vs ISO), declare authoritative data sources, set up OBE or dashboard metrics, or align reporting with contractual definitions. Requires the project EIM and ideally the Data Interface Register and Obligation Matrix (run those skills first if missing).
---

# KPI Definitions & Measurement Basis — Facilitated Session

Template: `KPI_Definitions/04-kpi-definitions.md` (repo root). The goal: one number per metric, no matter who computes it.

## Prerequisites

- Project EIM. Strongly preferred: Data Interface Register (source references) and Obligation Matrix (contractual definitions). If those don't exist, either run their skills first or capture their inputs ad hoc and flag the dependency.

## Session flow

1. **Select the KPI set.** Start from the template's standard 15; ask which are contractual on this project (cross-reference the Obligation Matrix), which are wanted for management, and which to drop. Contractual KPIs get full sheets first.
2. **Per KPI, run the sheet.** The hard fields are: measurement boundary, authoritative source, aggregation, timezone/interval convention, and exclusions. Push for precision — "availability" without an exclusions list is not a definition.
3. **Source-of-truth decisions.** For each quantity reported by multiple systems (SOC, power, availability, capacity), force an explicit declaration of the authoritative hop, with rationale. Use the EIM chain to enumerate the candidates.
4. **Reconciliation rules.** Agree tolerances and a cadence for cross-checking sources (e.g., monthly APM vs. LTSA vs. OMS availability; meter vs. PPC energy). These become standing data-quality checks.

## Facilitation guidance

- Whenever the contractual definition and the operational habit differ ("the LTSA reports availability excluding derates but the contract is capacity-weighted"), record both and mark the contractual one authoritative for money.
- Timezone and interval-convention errors (interval-beginning vs. -ending, DST) are the most common silent KPI corruption — ask explicitly per market.
- Tie each sheet to implementation: where will the official number be computed (dbt model, dashboard, report script)?

## Outputs

1. `04-kpi-definitions-{{project}}-v{{n}}.md` — sheets for all priority KPIs.
2. Implementation backlog: KPIs lacking a source or computation home.
3. Offer next steps: definitions feed the Monthly Report and Outage Accounting skills directly.
