---
type: Template
title: Warranty Obligation Matrix
description: "Enumerates every defect-style warranty (workmanship, materials, equipment), its claim path, and the owner-side conditions that keep coverage valid."
---

# Warranty Obligation Matrix

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> Purpose: enumerate every **defect-style warranty** — workmanship, materials, equipment defects — its term, remedy, claim path, and the owner-side conditions that keep coverage valid. Measured performance levels tied to LDs (availability, capacity, RTE, dispatch) live in the **Performance Guarantee Matrix**; this document covers "it broke / it was built wrong", not "it underperformed".

## 1. Warranty Inventory

| ID | Instrument | Counterparty | EIM Node | Term (start–end / trigger) | Coverage | Remedy (repair / replace / re-perform) | Cap / Limits | Claim Path | Contract Ref |
|----|-----------|--------------|----------|----------------------------|----------|----------------------------------------|--------------|------------|--------------|
| W-01 | Battery defect warranty | Battery OEM | `BAT_OEM_W` | | Defect-free workmanship/materials | Repair/replace | | via LTSA | |
| W-02 | PCS defect warranty | PCS OEM | `PCS_WAR` | | Defect-free operation | Repair/replace | | via LTSA | |
| W-03 | EMS/controller warranty | EMS OEM | `EMS_WAR` | | Software/hardware defects | Repair/patch | | EMS ticketing | |
| W-04 | BESS EPC workmanship warranty | BESS EPC | `EPC_BESS_WAR` | | Workmanship defects, site-level | Re-perform/repair | | direct | |
| W-05 | Substation EPC workmanship warranty | Substation EPC | `EPC_SUB_WAR` | | Workmanship defects | Re-perform/repair | | direct | |
| W-06 | BOP equipment warranties (transformers, switchgear, HVAC…) | Equipment OEMs | `BOP_WAR` | | Equipment defects | Repair/replace | | via EPC or direct | |
| W-07 | Serial defect provisions | Battery/PCS OEM | | | Fleet-wide defect threshold | Campaign replacement | | | |
| W-08 | Insurance policies | Insurer | `INS` | | Risk transfer (n/a defect) | Indemnity | | via AM | |

## 2. Owner-Side Obligations (conditions that keep warranties valid)

Warranties cut both ways — log what the owner/operator must do to keep coverage. Breaching a condition silently is the most expensive data-gap there is.

| Instrument | Obligation | How Monitored | Data Source | Current Status |
|-----------|-----------|---------------|-------------|----------------|
| W-01 | Operating temperature envelope | | | |
| W-01 | Stay within cycles/throughput limits (also conditions the performance guarantees) | | | |
| W-01–03 | PM performed per OEM schedule | LTSA CMMS | | |
| W-01–03 | Firmware kept at approved versions | | | |
| | SOC operating window restrictions | | | |
| | Storage/commissioning conditions honored (pre-COD) | | | |

## 3. Claims Log

| # | Date | Instrument | Defect / Issue | Evidence Pack (data, RCA, photos) | Submitted By | Status | Outcome |
|---|------|-----------|----------------|-----------------------------------|--------------|--------|---------|
| | | | | | | | |

## 4. Calendar — Key Dates

| Date | Event (warranty expiry, defect notification deadline, inspection window) | Instrument | Action Owner |
|------|--------------------------------------------------------------------------|-----------|--------------|
| | | | |

---
*Related: the [Performance Guarantee Matrix](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) holds all measured-performance guarantees (availability, capacity/retention, RTE, dispatch) and their calculation methodologies.*
