---
name: outage-availability-accounting
title: Outage & Availability Accounting — Facilitated Session
type: Skill
description: Build or update a BESS project's outage and availability accounting procedure through a facilitated session with the asset manager. Use whenever the user wants to reconcile availability numbers between the LTSA CMMS, ISO OMS, and the data platform/APM; define outage categories and exclusions (planned, forced, grid, force majeure, owner-directed); set partial-outage/derate accounting rules; prepare or contest LD calculations; or resolve disputes where different parties report different availability. Requires the project EIM, KPI Definitions (availability formulas), and Obligation Matrix (availability guarantees) — run those skills first if missing.
---

# Outage & Availability Accounting — Facilitated Session

Template: `Outage_Availability_Procedure/06-outage-availability-procedure.md` (repo root). The core problem this solves: three ledgers (ISO OMS, LTSA CMMS, measured telemetry) that never naturally agree, feeding LD calculations that very much need to.

## Prerequisites

- Project EIM (identifies the three ledgers and who writes them: per the map, GOP → SC → OMS; LTSA → CMMS; telemetry → APM).
- KPI Definitions for the availability formula variants; Obligation Matrix for which guarantee each variant serves. Capture ad hoc if missing, and flag.

## Session flow

1. **Taxonomy.** Walk the outage categories: confirm definitions, which guarantees each category counts against, the planned-outage allowance, and the evidence required per category. The contested categories (grid/external, force majeure, owner-directed) deserve the most time — get the contractual definitions verbatim.
2. **Capacity accounting rules.** Fixed rated basis or MAXENER-tracking? How are partial outages weighted, at what interval? What threshold opens a derate event? These choices move availability by whole percentage points — make the user choose deliberately.
3. **Three-ledger reconciliation.** Confirm who writes each ledger and at what granularity, then agree the monthly matching procedure, timestamp tolerance, and what happens to each flag type (unlogged outage, paper outage, category mismatch).
4. **Calculation variants.** Restate each contractual availability formula side by side with exclusions. The reconciled number is declared the single published figure.
5. **Backfill test.** Take last month's real events and run them through the draft procedure live. Nothing exposes a broken taxonomy faster.

## Facilitation guidance

- Watch for the classic conflict: the LTSA categorizes generously in its own favour. The procedure's job is to make categorization evidence-based, not adversarial — anchor every category on required evidence.
- Derates are where availability quietly leaks. If the project only tracks full outages today, flag derate detection as platform work (measured APD vs. rated).
- Ask who arbitrates a disputed categorization, and within what window — write it down.

## Outputs

1. `06-outage-procedure-{{project}}-v{{n}}.md` — populated template.
2. The backfill test results (last month reconciled) as a worked example appended to the doc.
3. Automation backlog: matching script, derate detector, monthly reconciliation report.
