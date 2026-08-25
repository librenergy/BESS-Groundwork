---
type: Template
title: RACI Matrix — Operational Events, Processes & Escalation
description: "Defines who is Responsible, Accountable, Consulted, and Informed for each operational process and event, derived from the EIM — and turns the accountable parties into the escalation & communication plan: severity levels, who-calls-whom chains, standing communications, and contractual notification deadlines."
---

# RACI Matrix — Operational Events, Processes & Escalation

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Companion docs:** Contact Register (phone numbers)

> Purpose: for every recurring operational process and event type, define who is **R**esponsible (does the work), **A**ccountable (owns the outcome, one per row), **C**onsulted, and **I**nformed. Entities are the green (company) nodes on the EIM. Sections 6–10 turn the accountable parties into the escalation & communication plan: when an event occurs, who calls whom, in what order, within what deadline.

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

## 6. Severity Levels

Escalation sections (6–10) are derived from the EIM communication edges and the RACI accountable parties above. Phone numbers live in the Contact Register.

| Level | Definition | Examples | Initial Notification Window |
|-------|-----------|----------|------------------------------|
| SEV-1 | Safety event, fire/thermal event, full plant trip, grid security impact | | immediate |
| SEV-2 | Major capacity loss (> {{X}}%), telemetry loss to ISO, dispatch non-compliance | | ≤ {{N}} min |
| SEV-3 | Single enclosure/PCS out, degraded redundancy | | same day |
| SEV-4 | Minor faults, data quality issues, no market impact | | next business day |

## 7. Escalation Chains by Event Type

For each: First Call → Backup → Accountable → Notify. The Accountable column must match the A in the matching RACI row above.

| Event Type | Detected By (system) | First Call | Backup | Accountable (RACI) | Must Notify | Deadline / Basis |
|-----------|----------------------|------------|--------|--------------------|-------------|------------------|
| Plant trip / breaker open | PPC / RTAC alarms | ROCC | GOP on-call | GOP | TOP, SC, AM, AO | |
| Fire / thermal runaway alarm | BMS / fire panel | 911 + ROCC | LTSA on-call | GOP | AO, AM, INS, Battery OEM | insurer notice clause |
| Battery fault (string offline) | Master BMS → APM | LTSA | Battery OEM support | LTSA | AM | |
| PCS fault | Inverter controller | LTSA | PCS OEM support | LTSA | AM | |
| EMS failure / dispatch loss | EMS watchdog | EMS OEM ticketing | GOP manual mode | GOP | SC, OPT, AM | |
| ISO telemetry fault (APD/APC/SOC/MAXENER stale or implausible) | APM data quality checks | GOP | SC → ISO help desk | GOP | AM, PE | ISO telemetry standards |
| Forced outage | any | GOP | | SC (OMS submission) | ISO via OMS, OPT | ISO outage reporting window: {{WINDOW}} |
| Substation / HV protection trip | P&C relays via SCADA | Sub LTSA | TOP | GOP | AO, AM, TO | |
| Revenue meter anomaly | settlement shadow checks | PE / AM | SC | AM | ISO settlements | dispute deadline: {{DEADLINE}} |
| Settlement dispute | shadow settlement | SC | | AM | AO | ISO dispute window |
| Cybersecurity incident | monitoring stack | {{IT/OT_SEC}} | GOP | AO | per IR plan, possibly regulator | |
| Weather event (prep) | forecast | GOP | LTSA | GOP | AM, INS | |

## 8. Standing Communications

From the EIM's recurring edges:

| Cadence | Meeting / Report | Parties | Owner | Content |
|---------|------------------|---------|-------|---------|
| Daily | Optimizer call | OPT, AM | OPT | dispatch plan, market conditions |
| Weekly | Optimizer report | OPT → AM | OPT | revenue, performance vs. plan |
| Weekly | O&M call | LTSA, AM, GOP | AM | open tickets, planned work |
| Monthly | Asset performance report | AM → AO, IL | AM (+ PE inputs) | see Monthly Report template |
| Quarterly | Lender / IE review | AM, IE, IL | AM | |
| Per event | Incident report (SEV-1/2) | per chain above | GOP/LTSA | RCA within {{N}} days |

## 9. Contractual Notification Obligations

| Trigger | Notify | Deadline | Method | Contract Ref |
|---------|--------|----------|--------|--------------|
| Insurable event | Insurer (via AM) | | written | policy |
| Warranty-relevant defect discovered | OEM (via LTSA) | | claim form | warranty |
| Force majeure | Offtaker, Lender | | written | tolling agmt |
| Capacity test failure | Offtaker, Lender, IE | | | |
| Change of control / refinancing events | various | | | |

## 10. Contact Quick Card (print-friendly)

| Role | Name | 24/7 Phone | Email |
|------|------|-----------|-------|
| ROCC | | | |
| GOP on-call | | | |
| LTSA on-call | | | |
| TOP RT desk | | | |
| SC desk | | | |
| ISO help desk | | | |
| AM | | | |

## Conventions

- Exactly one **A** per row. If two entities both appear accountable, the row needs to be split or the contract clarified — flag it.
- Where the EIM shows a flow that contradicts the contract (or vice versa), record it in **Discrepancies** below and resolve.
- Every escalation chain's Accountable (§7) must match the A of the corresponding RACI row; a mismatch is a Discrepancies entry.
- The escalation sections reference the Contact Register for numbers — never copy them here except into the quick card (§10), which is regenerated from the register on each revision.

## Discrepancies & Open Questions

| # | Row | Issue | Resolution Owner | Status |
|---|-----|-------|------------------|--------|
| | | | | |
