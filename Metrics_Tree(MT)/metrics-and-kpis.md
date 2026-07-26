---
type: Template
title: Metrics & KPIs
description: "Metric definitions (the authoritative calculations) and the KPI set (metrics held against targets) — companion to the metrics tree."
---

# Metrics & KPIs

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}` · **Companion:** the [metrics tree](./metrics-tree.md) shows how everything below rolls up.

> **Metric vs KPI.** A **metric** is a calculated quantity with exactly one authoritative definition — formula, data source, boundary, clock — so the APM, the OEM, the offtaker, and the monthly report produce the *same number*. A **KPI** is a metric held against a **target**: the health check that says whether the project is performing. Every KPI is a metric; most metrics are not KPIs. Where a quantity has **two contractual definitions** (availability and RTE routinely differ between the offtake contract and the LTSA — formula, boundary, exclusions, evaluation clock), define **two metrics, one per contract**, each with its own sheet and reporting chain; never merge them.

## 1. Metric definition sheets

One sheet per metric, starting with the money-critical ones — the availability metric(s), one per contract, belong first. Event tagging that feeds them lives in [BESS GADS](../BESS_GADS%28GADS%29/bess-gads.md); this document owns the formulas.

### `{{Metric Name}}`

| Field | Value |
|-------|-------|
| **kpi_code** | platform/application code — assign per the taxonomy ID scheme; blank until ratified |
| **Definition (plain language)** | |
| **What it is / is not** | one line on what it measures and the near-miss quantities it explicitly is *not* (capability vs delivered energy vs setpoint-following are classic confusions) |
| **Formula** | |
| **Units** | |
| **Measurement boundary** | POI / PCS AC terminals / DC bus / enclosure |
| **Authoritative data source** | system + tag (DIR interface ref) |
| **Secondary / cross-check source** | |
| **Sampling rate** | |
| **Aggregation** | how raw → interval → daily → monthly |
| **Timezone & interval convention** | e.g. interval-beginning, prevailing time; state the contract clock (Contract Year vs Operating Year) |
| **Exclusions** | planned outage, FM, grid events, test periods — per the governing contract |
| **Contractual reference** | PGM ID + clause (if applicable) |
| **Owner of the calculation** | who computes the official number |
| **Reporting chain** | who produces the official/first report, on what cadence and contractual deadline, and how the owner's shadow double-checks it (deemed-acceptance windows make this urgent) |
| **Known discrepancies between sources** | |

## 2. Starter metric set

These are **metrics** (authoritative calculations); attach a target in §3 to promote one to a KPI. Prune/extend per the project's contracts.

| kpi_code | Metric | Category | Contractual? |
|----------|--------|----------|--------------|
| | Availability — offtake-contract definition | Availability | Y |
| | Availability — LTSA/OEM definition | Availability | Y |
| | Excused-event log quality / notice compliance | Outage accounting | evidence duty |
| | Usable Energy Capacity / retention (MWh) | Capacity/SOH | Y |
| | State of Health (%) | Capacity/SOH | informational |
| | Round-Trip Efficiency — per contract definition(s) | Efficiency | Y |
| | Auxiliary Load (MWh, % of throughput) | Efficiency | N |
| | Throughput (MWh) / Equivalent Cycles vs limits | Usage | warranty condition |
| | Dispatch / AGC Compliance (%) | Market | Y |
| | Telemetry Accuracy & Uptime | Data Quality | Y |
| | SOC Imbalance / String Spread | Battery Health | leading indicator |
| | MTTR / Mean Time To Respond | O&M | SLA |
| | Forced Outage Rate by attribution class | Availability | derived |
| | Revenue / payment walk, LD accrual-vs-recovery ledger | Financial roll-up | reporting |

## 3. KPIs — is the project performing?

The short list reviewed monthly: each row is a §2 metric held against a target.

| kpi_code | KPI | Metric (§1/§2) | Target | Cadence |
|----------|-----|----------------|--------|---------|
| | | | | |

## 4. Source-of-truth decision table

Where multiple systems report the "same" quantity, declare the winner.

| Quantity | Candidate sources (per EIM) | Authoritative | Rationale |
|----------|------------------------------|---------------|-----------|
| SOC / energy state | BMS → site controller → offtaker telemetry | | each hop can transform — pick the hop; watch % vs MWh units |
| Active power at boundary | PCS, PPC, revenue meter | | revenue meter for $; PPC for control |
| Availability | OEM CMMS, offtaker/ISO view, owner event log | | owner event log, reconciled monthly (see BESS GADS) |
| Capacity (MWh) | live telemetry, capacity test, BMS estimate | | tests move money; telemetry predicts tests |
| Market/benchmark prices | counterparty statements, independent feed | | never verify a counterparty's price with the counterparty's number |

## 5. Reconciliation rules

- Monthly: owner-derived availability vs OEM-claimed vs offtaker/ISO view — document deltas > `{{TOLERANCE}}`; contest within contract notice windows.
- Telemetry plausibility: available power ≤ rated/guaranteed; SOC ≤ usable capacity; capacity signals change only on availability/SOH events (investigate steps); contractual flags logged as events, never dropped.
- Meter sanity: revenue-meter interval data vs PPC integrated power within `{{TOLERANCE_%}}`; watch sentinel/rollover values.
- Clock discipline: each contract's metrics on that contract's evaluation clock — never annualize across the wrong one.

## Open items

Tracked in this folder's `todo.md` (create it with the first item).
