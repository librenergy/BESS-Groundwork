---
type: Template
title: Performance Guarantee & Warranty Obligation Matrix
description: "Enumerates every contractual instrument that guarantees a measurable outcome, how it is measured, and the data that proves compliance."
---

# Performance Guarantee & Warranty Obligation Matrix

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> Purpose: enumerate every contractual instrument that guarantees a measurable outcome, the metric it guarantees, how it's measured, which data proves compliance, and the claim path. The data column is the Performance Engineering hook: each guaranteed metric must map to an interface in the Data Interface Register.

## 1. Instrument Inventory

| ID | Instrument | Counterparty | EIM Node | Term (start–end) | Guaranteed Metric(s) | Remedy Type (LDs / repair / replace / payout) | Cap / Limits | Claim Path | Contract Ref |
|----|-----------|--------------|----------|------------------|----------------------|-----------------------------------------------|--------------|------------|--------------|
| W-01 | Battery defect warranty | Battery OEM | `BAT_OEM_W` | | Defect-free workmanship/materials | Repair/replace | | via LTSA | |
| W-02 | Battery capacity warranty | Battery OEM | `BAT_OEM_W` | | Usable energy vs. degradation curve | Capacity payments / augmentation | | via LTSA | |
| W-03 | Battery OEM availability guarantee | Battery OEM | `BAT_OEM_SA` | | Battery availability % | LDs | | via LTSA | |
| W-04 | PCS warranty | PCS OEM | `PCS_WAR` | | Defect-free operation | Repair/replace | | via LTSA | |
| W-05 | PCS service agreement | PCS OEM | `PCS_SA` | | Response time, availability | Service credits | | via LTSA | |
| W-06 | EMS warranty / SA | EMS OEM | `EMS_WAR` / `EMS_SA` | | Uptime, response time, dispatch accuracy | Service credits | | EMS ticketing | |
| W-07 | LTSA availability guarantee | BESS LTSA | `BESS_LTSA` | | Plant/BESS availability % | LDs / bonus | | direct | |
| W-08 | BESS EPC workmanship warranty | BESS EPC | `EPC_BESS_WAR` | | Workmanship defects | Repair | | LTSA manages | |
| W-09 | Substation EPC workmanship warranty | Substation EPC | `EPC_SUB_WAR` | | Workmanship defects | Repair | | Sub LTSA manages | |
| W-10 | Tolling agreement performance obligations | Offtaker | `OFFTAKER` | | Availability, capacity, RT efficiency, response | LDs / revenue reduction | | direct | |
| W-11 | Insurance policies | Insurer | `INS` | | n/a (risk transfer) | Indemnity | | via AM | |

## 2. Metric → Measurement → Data Source Mapping

For each guaranteed metric, the precise measurement basis. Disagreements here are where disputes are born — be exact.

| Metric | Instrument(s) | Contractual Definition (clause ref) | Formula / Methodology | Measurement Boundary (POI? PCS terminals? DC?) | Authoritative Data Source (system + tag) | Interface Ref (DIR) | Sampling / Aggregation | Exclusions |
|--------|---------------|--------------------------------------|------------------------|------------------------------------------------|------------------------------------------|---------------------|------------------------|------------|
| Availability (%) | W-03, W-07, W-10 | | | | | | | planned outage, FM, grid events… |
| Usable Energy Capacity (MWh) | W-02, W-10 | | capacity test procedure ref | | | | | |
| Round-trip efficiency (%) | W-10 | | | | POI revenue meters | | | aux load treatment |
| Degradation curve compliance | W-02 | | | | | | | cycle/throughput conditions |
| Response time / ramp compliance | W-06, W-10 | | | | | | | |
| Throughput / cycle limits (warranty conditions) | W-02 | | | | | | | obligations on **owner** side |

## 3. Owner-Side Obligations (conditions that keep warranties valid)

Warranties cut both ways — log what the owner/operator must do to keep coverage.

| Instrument | Obligation | How Monitored | Data Source | Current Status |
|-----------|-----------|---------------|-------------|----------------|
| W-02 | Stay within cycles/year & throughput limits | | | |
| W-02 | Operating temperature envelope | | | |
| W-01–05 | PM performed per OEM schedule | LTSA CMMS | | |
| | SOC operating window restrictions | | | |
| | Firmware kept at approved versions | | | |

## 4. Claims Log

| # | Date | Instrument | Issue | Evidence Pack (data, RCA) | Submitted By | Status | Outcome |
|---|------|-----------|-------|---------------------------|--------------|--------|---------|
| | | | | | | | |

## 5. Calendar — Key Dates

| Date | Event (expiry, test window, renewal, notice deadline) | Instrument | Action Owner |
|------|-------------------------------------------------------|-----------|--------------|
| | | | |

---
*Change log:*

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | | | Initial draft from EIM |
