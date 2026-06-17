---
type: Template
title: Outage & Availability Accounting Procedure
description: "Reconciles the ISO OMS, LTSA CMMS, and measured availability records so numbers agree and LD calculations are defensible."
---

# Outage & Availability Accounting Procedure

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Companion docs:** KPI Definitions (availability KPIs), Obligation Matrix (availability guarantees)

> Purpose: reconcile the three parallel outage records that exist on every project — ISO OMS (market view), LTSA CMMS (contractual view), and the data platform / APM (measured view) — so availability numbers agree, exclusions are applied consistently, and LD calculations are defensible.

## 1. Outage Taxonomy

| Category | Code | Definition | Counts Against (LTSA avail / OEM avail / tolling avail / ISO) | Evidence Required |
|----------|------|-----------|---------------------------------------------------------------|-------------------|
| Planned maintenance | PO | scheduled ≥ {{N}} days ahead, within annual allowance | per contract | CMMS WO + approval |
| Forced — battery | FO-BAT | | OEM + LTSA | BMS logs, RCA |
| Forced — PCS | FO-PCS | | | inverter logs |
| Forced — BOP/substation | FO-BOP | | Sub LTSA | relay records |
| Forced — EMS/controls | FO-EMS | | EMS SA | EMS logs |
| Grid / external | EXT | grid outage, TOP directive, transmission | excluded (usually) | TOP/ISO correspondence |
| Force majeure | FM | per contract definition | excluded | declaration + evidence |
| Owner-directed | OD | testing, augmentation | excluded from LTSA | AO instruction |
| Derate (partial) | DR | capacity reduction without full outage | pro-rata | telemetry |

## 2. Capacity Accounting Rules

- Plant rated capacity basis: `{{MW}}` / `{{MWH}}` — fixed or MAXENER-tracking? Define: `{{BASIS}}`
- Partial outages: availability weighted by `available capacity / rated capacity` per interval of `{{INTERVAL}}`.
- Derate detection: measured from `{{SOURCE — e.g., APD vs rated}}`; threshold `{{X}}` MW for `{{Y}}` minutes opens a derate event.
- Aux consumption during outage: `{{TREATMENT}}`.

## 3. The Three Ledgers & Reconciliation

| Ledger | System (EIM node) | Written By | Granularity | Used For |
|--------|--------------------|-----------|-------------|----------|
| Market | ISO OMS (`OMS`) | SC (from GOP) | per ISO rules | ISO compliance, market availability |
| Contractual | LTSA CMMS (`CMMS`) | LTSA | work order | LD / bonus calculation |
| Measured | Data platform / APM (`APM`) | automated from telemetry | interval | ground truth |

**Monthly reconciliation procedure:**
1. Export the month's events from all three ledgers.
2. Match events by overlap (timestamp tolerance `{{TOL}}`).
3. Flag: events in measured-but-not-CMMS (unlogged outages), CMMS-but-not-measured (paper outages), category mismatches.
4. Resolve flags in the monthly O&M call; record dispositions below.
5. Publish the reconciled availability number — this is the figure used in the Monthly Report and LD calculations.

## 4. Availability Calculation (authoritative)

> Pull formulas from KPI Definitions; restate the contractual variants side by side.

| Variant | Formula | Exclusions Applied | Used By |
|---------|---------|--------------------|---------|
| LTSA contract availability | | | LD calc |
| Battery OEM availability | | | OEM guaranty |
| Tolling availability | | | offtaker |
| Raw measured availability | | none | trending |

## 5. Outage Event Log (running)

| # | Start | End | Category | Capacity Affected (MW/MWh) | Description | CMMS WO | OMS Ref | Measured Ref | Contractual Disposition | RCA Link |
|---|-------|-----|----------|----------------------------|-------------|---------|---------|--------------|-------------------------|----------|
| | | | | | | | | | | |

## 6. Disputes & Adjustments

| # | Period | Issue | Positions (LTSA vs AO vs measured) | Resolution | Impact (LD $ / avail %) |
|---|--------|-------|-------------------------------------|------------|--------------------------|
| | | | | | |

---
*Change log:*

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | | | Initial draft from EIM |
