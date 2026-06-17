---
type: Template
title: Event Escalation & Communication Plan
description: "When an event occurs, who calls whom, in what order, within what deadline — derived from the EIM communication edges and RACI."
---

# Event Escalation & Communication Plan

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Companion docs:** Contact Register, RACI Matrix

> Purpose: when something happens, who calls whom, in what order, within what deadline. Derived from the EIM communication edges and the RACI accountable parties.

## 1. Severity Levels

| Level | Definition | Examples | Initial Notification Window |
|-------|-----------|----------|------------------------------|
| SEV-1 | Safety event, fire/thermal event, full plant trip, grid security impact | | immediate |
| SEV-2 | Major capacity loss (> {{X}}%), telemetry loss to ISO, dispatch non-compliance | | ≤ {{N}} min |
| SEV-3 | Single enclosure/PCS out, degraded redundancy | | same day |
| SEV-4 | Minor faults, data quality issues, no market impact | | next business day |

## 2. Escalation Chains by Event Type

For each: First Call → Backup → Accountable → Notify. Phone numbers live in the Contact Register.

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

## 3. Standing Communications

From the EIM's recurring edges:

| Cadence | Meeting / Report | Parties | Owner | Content |
|---------|------------------|---------|-------|---------|
| Daily | Optimizer call | OPT, AM | OPT | dispatch plan, market conditions |
| Weekly | Optimizer report | OPT → AM | OPT | revenue, performance vs. plan |
| Weekly | O&M call | LTSA, AM, GOP | AM | open tickets, planned work |
| Monthly | Asset performance report | AM → AO, IL | AM (+ PE inputs) | see Monthly Report template |
| Quarterly | Lender / IE review | AM, IE, IL | AM | |
| Per event | Incident report (SEV-1/2) | per chain above | GOP/LTSA | RCA within {{N}} days |

## 4. Contractual Notification Obligations

| Trigger | Notify | Deadline | Method | Contract Ref |
|---------|--------|----------|--------|--------------|
| Insurable event | Insurer (via AM) | | written | policy |
| Warranty-relevant defect discovered | OEM (via LTSA) | | claim form | warranty |
| Force majeure | Offtaker, Lender | | written | tolling agmt |
| Capacity test failure | Offtaker, Lender, IE | | | |
| Change of control / refinancing events | various | | | |

## 5. Contact Quick Card (print-friendly)

| Role | Name | 24/7 Phone | Email |
|------|------|-----------|-------|
| ROCC | | | |
| GOP on-call | | | |
| LTSA on-call | | | |
| TOP RT desk | | | |
| SC desk | | | |
| ISO help desk | | | |
| AM | | | |

---
*Change log:*

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | | | Initial draft from EIM |
