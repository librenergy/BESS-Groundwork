---
name: performance-guarantee-matrix
title: Performance Guarantee Matrix — Facilitated Session
type: Skill
description: Build or update a BESS project's performance guarantee matrix from its contracts and Entity Interaction Map through a facilitated session with the asset manager or owner. Use whenever the user wants to inventory measured-performance guarantees (availability, capacity/energy retention, RTE, dispatch compliance) across the offtake/tolling agreement, LTSA/OEM service agreements, and O&M contracts; pin down exactly how each guaranteed number is calculated; compare what is excused under one contract but not another; or set up shadow calculations for LD verification. Requires a project EIM (run eim-review-build first if none exists); defect-style warranties belong to the warranty-obligation-matrix skill instead.
---

# Performance Guarantee Matrix: Facilitated Session

> **Before you start:** read `Definitions(DEF)/definitions.md` (what every acronym and term means on this project; ⚠️-flagged entries carry two meanings) and `Ontology(ONT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Template: `Performance_Guarantee_Matrix(PGM)/performance-guarantee-matrix.md` (repo root). The engineering payload is the **calculation methodology**, not the LD rates: for every guarantee, extract exactly how the number is computed, from which data, with which exclusions; then expose where the contracts are asymmetric. Whoever can reproduce the counterparty's number wins the dispute.

## Scope boundary

Measured performance levels tied to LDs or payment adjustments: availability, capacity/energy retention, efficiency, dispatch/response compliance, throughput conditions. **Defect warranties (workmanship, materials) belong to the Warranty Obligation Matrix**; if the user brings those up, note them and hand off.

## Prerequisites

- Project EIM (offtake, LTSA/OEM, O&M branches identify the instruments).
- The actual contracts or their guarantee exhibits; this skill is contract extraction, not recollection. If a referenced document is missing (e.g., an SPA defining guaranteed capacity), record it as an open item; do not guess values.

## Session flow

### Step 1: Guarantee inventory
One row per guaranteed metric per contract, in both directions: guarantees the owner **owes** (offtake/tolling) and guarantees the owner **holds** (LTSA, OEM, O&M SLAs, interconnection obligations). For each: guaranteed level (with year-by-year tables transcribed in full), evaluation period, assessment basis (continuous telemetry vs periodic test vs event log), LD/remedy and caps (recorded for reference).

### Step 2: One calculation sheet per guarantee (the core)
For each row, produce a standalone `calculations/PG-{ID}-{contract}-{metric}.md` (e.g. `PG-101-tolling-storage-availability.md`) so it can be reviewed independently. Each sheet must answer completely: evaluation period and cadence; the exact formula with every defined term resolved to its source; measurement boundary (POI meter / POM / inverter terminals / controller estimate) and loss adjustments between boundaries; **every** condition and exclusion (excuse events, notice requirements, caps on exclusions, data-gap rules, temperature/voltage adjustment tables, test preconditions, deemed values, freeze/termination triggers); edge cases; the data required to shadow the calculation; reporting cadence and review/dispute windows. Where the contract is silent on methodology, flag it loudly: silent methodology is a future dispute.

For **delivery-based guarantees** (dispatch/setpoint compliance, as opposed to capability-based availability), the sheet must also document the **control chain and loop closure point**: how the offtaker's setpoint reaches the plant (dispatch signal → RTUs/RTACs → plant controller → OEM site controller), and at which meter the control loop actually closes versus where compliance is measured. If the documented closed loop settles upstream of the compliance meter (e.g. at the POM or inverter terminals while compliance is judged at the POI), the losses between them silently consume part of the compliance band: a systematic exposure, not an operational one. Where no document shows a loop closing on the compliance meter, record that as an open ask to the controls integrator.

Each sheet also carries a **`## Pseudo-SQL`** section: one illustrative query showing how the calculation would look against the shared placeholder schema in [`calculations/index.md`](/Performance_Guarantee_Matrix%28PGM%29/calculations/index.md), plus a short "easy to get wrong" note tying the SQL back to that guarantee's trap. Framing rules that keep it from becoming a false source of truth: illustrative not runnable; placeholder names pending the Data Interface Register; **the formula on the sheet wins on any disagreement**; unknown contractual constants appear as `contract_param` lookups marked ❓ rather than as literals. Writing them reliably surfaces which calculations read no telemetry at all, which inputs block on a document not held, and **which conditions cannot be expressed in SQL** (deemed values, notice-deadline tests); record those findings, they scope the build. Start from [`calc-sheet-template.md`](/Performance_Guarantee_Matrix%28PGM%29/calculations/calc-sheet-template.md).

### Step 3: Exclusions asymmetry (the money table)
Build the event-by-event comparison: for each outage/event class (supplier maintenance, BOP forced outage, controls/comms failure, one-sided capability loss, missed setpoints, grid events, force majeure, test hours, data gaps, owner delays, throughput over-use), record its treatment under each contract and the net owner exposure. Rows excused on the supplier side but counting on the offtake side are retained risk; make them impossible to miss. Compare force majeure definitions explicitly; they are never identical.

### Step 4: Shadow calculation requirements
Consolidate from the calc sheets: which event logs, telemetry series (with resolution), and derived metrics the platform must maintain to reproduce every counterparty number, including dual-classification outage logging when contracts use different availability mechanics (event-based vs telemetry-based). Feed the list into the Data Interface Register and the Metrics Tree; flag anything that must be capturing data **before commissioning**, because retroactive shadow calculation is impossible.

### Step 5: Calendar and tracker
Key dates: test windows (and their weather/consent constraints across contracts), report due dates, review/dispute deadlines, guarantee step-downs, throughput/termination projections. Migrate any live shortfalls into the LD tracker.

## Facilitation guidance

- Keep pulling the user back from LD rates to **calculation mechanics**: the rates are commercial history; the formulas, exclusions, and evaluation periods are what engineering must operationalize.
- Watch for structural traps seen in real contracts: annual averaging vs event accounting; max(charge, discharge) capability rules; self-assessed guarantees; per-day LD accrual from test-report dates; throughput-conditioned guarantee tables that step down or terminate; deemed-zero rules.
- When one physical test campaign serves multiple contracts, check whether the test conditions (temperature adjustments, seasonal consent) diverge between them; schedule where the bases align.

## Outputs

1. `Performance_Guarantee_Matrix(PGM)/performance-guarantee-matrix.md` populated in place (version bumped in the change log).
2. One `PG-{ID}-{contract}-{metric}.md` sheet per guarantee, individually reviewable.
3. The exclusions-asymmetry table → retained-risk register input.
4. Shadow-calculation requirements → Data Interface Register and the Metrics Tree skills; pre-COD data-capture punch list.
