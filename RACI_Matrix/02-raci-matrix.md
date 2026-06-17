---
type: Template
title: RACI Matrix — Operational Events & Processes
description: "Defines who is Responsible, Accountable, Consulted, and Informed for each operational process and event, derived from the EIM."
---

# RACI Matrix — Operational Events & Processes

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> Purpose: for every recurring operational process and event type, define who is **R**esponsible (does the work), **A**ccountable (owns the outcome, one per row), **C**onsulted, and **I**nformed. Entities are the green (company) nodes on the EIM.

## Entity Legend

| Code | Entity | EIM Node |
|------|--------|----------|
| AO | Asset Owner | `AO` |
| AM | Asset Manager | `AM` |
| GOP | Generator Operator | `GOP` |
| LTSA | BESS LTSA / O&M Provider | `BESS_LTSA` |
| ROCC | Remote Ops Control Center | `ROCC` |
| SLTSA | Substation LTSA / O&M | `SUB_LTSA` |
| SC | Scheduling Coordinator / QSE | `SC` |
| OPT | Optimizer | `OPT` |
| BOEM | Battery OEM | `BAT_OEM` |
| POEM | PCS OEM | `PCS_OEM` |
| EOEM | EMS OEM | `EMS_OEM` |
| EPC-B | BESS EPC | `EPC_BESS` |
| EPC-S | Substation EPC | `EPC_SUB` |
| TOP | Transmission Operator RT Desk | `TOP` |
| ISO | Balancing Authority / ISO | `BA` |
| IE | Independent Engineer | `IE` |
| INS | Insurer | `INS` |
| IL | Investors / Lender | `IL` |
| PE | Performance Engineer (consultant) | — |

## 1. Real-Time Operations

| Process / Event | AO | AM | GOP | LTSA | ROCC | SC | OPT | TOP | ISO | PE | Notes |
|-----------------|----|----|-----|------|------|----|----|-----|-----|----|-------|
| 24/7 plant monitoring | I | I | A | R | R | | | | | | |
| Plant trip — immediate response | I | I | A/R | R | R | I | I | C | I | | |
| Dispatch/AGC non-compliance | I | I | A | C | | R | C | I | I | C | |
| Telemetry signal fault (APD/APC/SOC/MAXENER) | I | I | A | R | | C | I | I | I | C | e.g. MAXENER stuck/incorrect |
| Curtailment / exceptional dispatch | I | I | A | | | R | C | C | I | | |
| Grid disturbance / oscillation event | I | I | A | C | | I | | C | I | R | analysis |

## 2. Outage Management

| Process / Event | AO | AM | GOP | LTSA | SLTSA | SC | OPT | ISO | PE | Notes |
|-----------------|----|----|-----|------|-------|----|----|-----|----|-------|
| Planned outage scheduling | A | C | R | R | R | C | C | I | | |
| Outage submission to ISO OMS | I | I | C | | | A/R | I | I | | per EIM: GOP → SC → OMS |
| Forced outage logging & categorization | I | A | R | R | R | I | | | C | feeds availability accounting |
| Outage return-to-service | I | I | A | R | R | R | I | I | | |

## 3. Maintenance & Warranty

| Process / Event | AO | AM | LTSA | SLTSA | BOEM | POEM | EOEM | EPC-B | EPC-S | PE | Notes |
|-----------------|----|----|------|-------|------|------|------|-------|-------|----|-------|
| Preventive maintenance execution | I | A | R | R | C | C | C | | | | |
| Corrective maintenance — battery | I | A | R | | R | | | | | C | |
| Corrective maintenance — PCS | I | A | R | | | R | | | | | |
| Corrective maintenance — EMS | I | A | R | | | | R | | | | |
| Warranty claim — battery defect/capacity | A | R | R | | C | | | | | R | PE provides evidence pack |
| Warranty claim — EPC workmanship | A | R | R | C | | | | C | C | C | LTSA manages per EIM |
| Spares management | I | A | R | R | C | C | C | | | | |
| Capacity test planning & witnessing | A | R | R | | C | | | | | R | |
| Firmware / control parameter changes (BMS, PCS, EMS) | A | C | R | | C | C | C | | | C | conditional-execution gate |

## 4. Market & Commercial

| Process / Event | AO | AM | SC | OPT | OFFTAKER | ISO | PE | Notes |
|-----------------|----|----|----|----|----------|-----|----|-------|
| Day-ahead bidding strategy | I | C | R | A/R | C | | | |
| Real-time bid adjustments | I | I | R | A/R | | | | |
| Settlement review & dispute | A | R | R | C | C | I | C | shadow settlement |
| Performance reporting to offtaker | A | R | | C | I | | C | |
| Weekly optimizer review | I | A/R | | R | | | | per EIM: daily call / weekly report |

## 5. Reporting & Compliance

| Process / Event | AO | AM | LTSA | IE | INS | IL | PE | Notes |
|-----------------|----|----|------|----|----|----|----|-------|
| Monthly asset performance report | A | R | C | I | | I | R | |
| Annual capacity / degradation report | A | R | C | C | | I | R | |
| Lender / IE reporting | A | R | | C | | I | C | |
| Insurance claims | A | R | C | | C | I | C | |
| NERC / regulatory compliance | A | C | C | | | | | GOP often R — confirm |

## Conventions

- Exactly one **A** per row. If two entities both appear accountable, the row needs to be split or the contract clarified — flag it.
- Where the EIM shows a flow that contradicts the contract (or vice versa), record it in **Discrepancies** below and resolve.

## Discrepancies & Open Questions

| # | Row | Issue | Resolution Owner | Status |
|---|-----|-------|------------------|--------|
| | | | | |

---
*Change log:*

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | | | Initial draft from EIM |
