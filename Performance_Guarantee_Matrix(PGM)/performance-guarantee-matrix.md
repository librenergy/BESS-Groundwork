---
type: Template
title: Performance Guarantee Matrix
description: "Enumerates every contractual guarantee of a measured performance level (availability, capacity/retention, efficiency, dispatch), the exact calculation methodology behind each, and the asymmetries in what is excused between contracts."
---

# Performance Guarantee Matrix

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> Purpose: enumerate every contractual instrument that guarantees a **measured performance level** — availability, capacity/energy retention, efficiency, dispatch/response — and pin down, for each one, *exactly how the number is calculated*: evaluation period, formula, measurement boundary, inputs, and every exclusion/excuse condition. The commercial terms (LD rates, caps) are recorded for reference, but the engineering payload is the calculation methodology and the data needed to shadow it. Defect-style warranties live in the **Warranty Obligation Matrix**; this document covers "it underperformed", not "it broke".

## 1. Guarantee Inventory

One row per guaranteed metric per contract. Typical sources: offtake/tolling agreement, LTSA/OEM service agreement, O&M/BOP contracts (SLAs), EPC (pre-COD performance tests), interconnection agreement (response/ramp obligations).

The PG-01…PG-08 rows are **seed examples** for a full-toll + LTSA structure — reshape or delete them to match the project's actual contract stack (merchant/PPA projects replace the offtake rows).

| ID | Guarantee | Contract | Counterparty | EIM Node | Direction (owed to / held by owner) | Guaranteed Level | Evaluation Period | Assessment Basis (telemetry / test / event log) | LD / Remedy (reference) | Cap | Calc Sheet |
|----|-----------|----------|--------------|----------|--------------------------------------|------------------|-------------------|--------------------------------------------------|--------------------------|-----|-----------|
| PG-01 | Availability | Offtake/tolling | Offtaker | | owed to | | annual | | | | |
| PG-02 | Capacity | Offtake/tolling | Offtaker | | owed to | | per test / monthly | | | | |
| PG-03 | Efficiency (RTE) | Offtake/tolling | Offtaker | | owed to | | per test / monthly | | | | |
| PG-04 | Dispatch compliance | Offtake/tolling | Offtaker | | owed to | | hourly / event | | | | |
| PG-05 | Availability | LTSA | BESS integrator | | held by | | annual | | | | |
| PG-06 | Energy retention / capacity | LTSA | BESS integrator | | held by | | per test | | | | |
| PG-07 | Efficiency (RTE) | LTSA | BESS integrator | | held by | | per test | | | | |
| PG-08 | O&M / BOP SLAs (response, availability) | O&M agreement | O&M provider | | held by | | | | | | |

## 2. Calculation Sheets

The core of this document. **One standalone sheet per guarantee**, so each can be reviewed, implemented, and disputed independently. Each sheet must answer, completely:

1. **What is guaranteed** — metric, level, direction.
2. **Evaluation period and cadence** — annual average? per test? monthly? When does the clock start?
3. **Exact formula** — transcribed from the contract, with every defined term resolved.
4. **Measurement boundary and source** — POI meter? inverter terminals? controller estimate? Which physical/logical point, which system produces the number, and any loss adjustments between boundaries.
5. **All conditions and exclusions** — every event class that is excused, excluded, or adjusts numerator vs denominator; notice requirements; caps on exclusions; data-gap handling; temperature/voltage adjustment tables; test preconditions.
6. **Edge cases and traps** — deemed values, one-sided measurements, freeze/termination triggers, proration rules.
7. **Data and metrics required to shadow it** — the tags, event logs, and derived series Performance Engineering must maintain to reproduce the counterparty's number.
8. **Reporting and verification** — who computes it, what report, when, and the review/dispute window.
9. **LD/remedy (reference)** — recorded but secondary.
10. **Open items** — undefined terms, missing referenced documents.

Sheets live in the `calculations/` subfolder as `calc-{contract}-{metric}.md`.

## 3. Exclusions & Excuse Asymmetry

The money question when the owner sits between an offtake guarantee (owed) and supplier guarantees (held): **which outage/event causes are excused on one side but not the other**. Every row where the answer differs is retained owner risk.

| Event / cause class | Treatment under offtake guarantee | Treatment under LTSA/OEM guarantee | Treatment under O&M SLA | Net owner exposure |
|---------------------|-----------------------------------|-------------------------------------|--------------------------|--------------------|
| Supplier planned maintenance | | | | |
| BOP planned maintenance | | | | |
| BOP forced outage (transformer, switchgear, gen-tie) | | | | |
| Controls/SCADA/comms outage (incl. failed dispatch commands) | | | | |
| BESS equipment fault | | | | |
| One-sided capability loss (charge-only or discharge-only) | | | | |
| Missed setpoint / dispatch deviation | | | | |
| Grid outage / curtailment / voltage excursion | | | | |
| Force majeure (compare definitions!) | | | | |
| Performance test hours | | | | |
| Telemetry/data gaps | | | | |
| Low SOC / energy state | | | | |
| Owner-caused delay (access, LOTO, approvals) | | | | |
| Throughput/cycling over-use | | | | |

## 4. Shadow Calculation Requirements

Consolidated from the calc sheets: the event logs, telemetry series, and derived metrics the platform must maintain to independently reproduce every guaranteed number. Feed these into the Data Interface Register and Metrics & KPIs.

| Requirement | Type (tag / event log / derived) | Serves Guarantee(s) | Resolution / Retention | Source System | Status |
|-------------|----------------------------------|----------------------|------------------------|---------------|--------|
| | | | | | |

## 5. LD & Claims Tracker (reference)

| # | Period | Guarantee | Reported Value | Shadow Value | Delta | LD Claimed / Owed | Status |
|---|--------|-----------|----------------|--------------|-------|--------------------|--------|
| | | | | | | | |

## 6. Calendar — Key Dates

| Date | Event (test window, report due, review/dispute deadline, guarantee step-down) | Guarantee | Action Owner |
|------|-------------------------------------------------------------------------------|-----------|--------------|
| | | | |

---
*Related: the [Warranty Obligation Matrix](../Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) holds defect-style warranties and the owner-side conditions that keep them valid.*
