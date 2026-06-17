---
type: Template
title: KPI Definition & Measurement Basis Document
description: "One authoritative definition per KPI — formula, data source, boundary, aggregation — so every system reports the same number."
---

# KPI Definition & Measurement Basis Document

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> Purpose: one authoritative definition per KPI — formula, data source, boundary, aggregation — so that the APM, the LTSA, the ISO, and the monthly report all produce the *same number*. Every KPI must trace to an interface in the Data Interface Register and, where contractual, to an instrument in the Obligation Matrix.

## KPI Definition Sheet (one per KPI)

### KPI-NN — `{{KPI Name}}`

| Field | Value |
|-------|-------|
| **Definition (plain language)** | |
| **Formula** | |
| **Units** | |
| **Measurement boundary** | POI / PCS AC terminals / DC bus / enclosure |
| **Authoritative data source** | system + tag (DIR interface ref) |
| **Secondary / cross-check source** | |
| **Sampling rate** | |
| **Aggregation** | how raw → interval → daily → monthly |
| **Timezone & interval convention** | e.g. interval-beginning, CPT for ERCOT |
| **Exclusions** | planned outage, FM, grid events, test periods |
| **Contractual reference** | Obligation Matrix ID + clause (if applicable) |
| **Owner of the calculation** | who computes the official number |
| **Where published** | dashboard / monthly report / settlement pack |
| **Known discrepancies between sources** | |

---

## Standard KPI Set (instantiate a sheet per row)

| ID | KPI | Category | Contractual? | Priority |
|----|-----|----------|--------------|----------|
| KPI-01 | Plant Availability (%) | Availability | Y (LTSA, tolling) | H |
| KPI-02 | Battery Availability (%) | Availability | Y (OEM guaranty) | H |
| KPI-03 | PCS Availability (%) | Availability | maybe | M |
| KPI-04 | Usable Energy Capacity (MWh) | Capacity/SOH | Y (capacity warranty) | H |
| KPI-05 | State of Health (%) | Capacity/SOH | informational | M |
| KPI-06 | Round-Trip Efficiency (%) | Efficiency | Y (tolling) | H |
| KPI-07 | Auxiliary Load (MWh, % of throughput) | Efficiency | N | M |
| KPI-08 | Throughput (MWh) / Equivalent Cycles | Usage | warranty condition | H |
| KPI-09 | Dispatch / AGC Compliance (%) | Market | ISO performance metrics | H |
| KPI-10 | Telemetry Accuracy & Uptime (APD/APC/SOC/MAXENER) | Data Quality | ISO requirement | H |
| KPI-11 | SOC Imbalance / Spread Across Strings | Battery Health | N (leading indicator) | M |
| KPI-12 | Mean Time To Respond / Repair (LTSA) | O&M | Y (SA response times) | M |
| KPI-13 | Forced Outage Rate | Availability | derived | M |
| KPI-14 | Revenue Capture vs. Benchmark | Commercial | N | M |
| KPI-15 | OBE — Overall Battery Effectiveness | Composite | N | M |

## Source-of-Truth Decision Table

Where multiple systems report the "same" quantity, declare the winner.

| Quantity | Candidate Sources (per EIM) | Authoritative | Rationale |
|----------|------------------------------|---------------|-----------|
| SOC | String BMS → Master BMS → Battery Controller → EMS → ISO telemetry | | each hop can transform — pick the hop |
| Active power at boundary | PCS, PPC, POI revenue meter | | revenue meter for $; PPC for control |
| Availability | LTSA CMMS, ISO OMS, APM derived | | reconcile monthly |
| Capacity (MWh) | MAXENER, capacity test, BMS estimate | | |

## Reconciliation Rules

- Monthly: APM-derived availability vs. LTSA-claimed vs. ISO OMS — document deltas > `{{TOLERANCE}}`.
- Telemetry plausibility: APD ≤ rated power; SOC ≤ MAXENER; MAXENER changes only on availability/SOH events (step changes investigated).
- Meter sanity: POI interval data vs. PPC integrated power within `{{TOLERANCE_%}}`; watch for sentinel/rollover values on revenue meters.

---
*Change log:*

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | | | Initial draft from EIM |
