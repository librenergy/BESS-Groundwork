---
type: Template
title: Monthly Asset Performance Report
description: "Monthly/quarterly asset performance reporting pack, assembled from the satellite documents and auditable back to source data per the EIM."
---

# Monthly Asset Performance Report — {{MONTH}} {{YEAR}}

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Prepared by:** `{{AUTHOR}}`  **Date:** `{{DATE}}`
**Distribution:** Asset Owner, Asset Manager, Lender (per Communication Plan)

> Each section lists its feeding system(s) per the EIM / Data Interface Register so the report is auditable back to source data.

## 1. Executive Summary

- Overall status: 🟢 / 🟡 / 🔴
- Headline numbers: availability `{{x}}%` (target `{{y}}%`), revenue `{{$}}` (vs. plan `{{$}}`), capacity `{{MWh}}` (warranty curve: `{{MWh}}`)
- Top 3 issues / actions this month:
  1.
  2.
  3.

## 2. Availability & Outages
*Sources: reconciled ledger (Outage Tracker §6) — owner platform + OEM CMMS + offtaker/ISO view*

| Metric | This Month | YTD | Target | Status |
|--------|-----------|-----|--------|--------|
| Plant availability (contract basis) | | | | |
| Battery availability (OEM basis) | | | | |
| Forced outage rate | | | | |
| Planned outage allowance used | | | | |

Significant outage events:

| Event | Duration | Category | MWh Impact | RCA Status |
|-------|----------|----------|-----------|------------|
| | | | | |

## 3. Capacity & Battery Health
*Sources: APM / data platform (BMS chain: String BMS → Master BMS), capacity tests, MAXENER history — ISO signal names are CAISO-style examples; adapt to the market*

| Metric | This Month | Trend | Warranty Curve | Margin |
|--------|-----------|-------|----------------|--------|
| Usable energy (MWh) | | | | |
| SOH (%) | | | | |
| MAXENER (ISO-reported) | | | | consistency check vs measured |
| Equivalent cycles (month / cumulative) | | | warranty limit | |
| Throughput (MWh) | | | | |
| SOC imbalance / string spread | | | | leading indicator |

Notes on degradation, balancing behaviour, calibration events:

## 4. Efficiency
*Sources: POI revenue meters, aux metering*

| Metric | This Month | YTD | Guarantee | Status |
|--------|-----------|-----|-----------|--------|
| Round-trip efficiency (POI basis) | | | | |
| Auxiliary consumption (MWh / %) | | | | |

## 5. Market Performance
*Sources: Trade & Award Data, Settlement System, Optimizer weekly reports*

| Metric | This Month | YTD | Plan | Status |
|--------|-----------|-----|------|--------|
| Gross revenue ($) | | | | |
| Revenue by stream (energy / AS / capacity) | | | | |
| Dispatch / AGC compliance (%) | | | | |
| ISO performance metrics (e.g., mileage, accuracy) | | | | |
| Settlement disputes open | | | | |

## 6. Telemetry & Data Quality
*Sources: data platform quality checks on the RTAC → ISO chain*

| Signal | Uptime % | Plausibility Failures | Open Issues |
|--------|----------|-----------------------|-------------|
| APD / APC | | | |
| SOC | | | |
| MAXENER | | | |
| Meter data completeness | | | |

## 7. O&M Activity
*Sources: LTSA ticketing + CMMS, EMS ticketing*

| Metric | This Month | Notes |
|--------|-----------|-------|
| Work orders opened / closed | | |
| Open corrective backlog | | |
| Mean time to respond / repair | | vs. SA commitment |
| Firmware / parameter changes executed | | with approvals |

Top open tickets:

| Ticket | System | Age | Severity | Status / Next Step |
|--------|--------|-----|----------|--------------------|
| | | | | |

## 8. Warranty & Contract Status
*Source: Warranty Obligation Matrix + Performance Guarantee Matrix + claims log*

| Item | Status |
|------|--------|
| Open warranty claims | |
| Availability guarantee position (YTD vs. threshold, projected LD/bonus) | |
| Capacity warranty position | |
| Owner-side obligation compliance (cycles, temperature, PM) | |
| Upcoming key dates (expiries, tests) | |

## 9. HSE & Compliance

| Item | Status |
|------|--------|
| Safety incidents | |
| Regulatory / NERC items | |
| Insurance matters | |

## 10. Actions & Decisions Required

| # | Item | Owner | Required By | Decision Needed From |
|---|------|-------|-------------|----------------------|
| | | | | |

## Appendix
- A. Reconciled outage event log (month)
- B. Capacity test results (if performed)
- C. Settlement reconciliation summary
- D. Dashboard snapshots
