---
type: Template
title: Performance Guarantee Matrix
description: "Enumerates every contractual guarantee of a measured performance level (availability, capacity/retention, efficiency, dispatch), the exact calculation methodology behind each, and the asymmetries in what is excused between contracts."
---

# Performance Guarantee Matrix

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`

> **Guarantee ID scheme (three digits, series by counterparty):** **PG-1xx** = offtaker · **PG-2xx** = LTSA/supply provider · **PG-3xx** = O&M provider; add further series (4xx…) per additional counterparty. Numbering starts at x01 within each series and is **append-only**: a new guarantee takes the next free number in its counterparty's series, so existing IDs never shift when one is inserted. The series follows the **counterparty**, not the instrument (a supply-agreement guarantee from the same vendor as the LTSA shares the 2xx series).

> Purpose: enumerate every contractual instrument that guarantees a **measured performance level**: availability, capacity/energy retention, efficiency, dispatch/response. It pins down, for each one, *exactly how the number is calculated*: evaluation period, formula, measurement boundary, inputs, and every exclusion/excuse condition. The commercial terms (LD rates, caps) are recorded for reference, but the engineering payload is the calculation methodology and the data needed to shadow it. Defect-style warranties live in the **Warranty Obligation Matrix**; this document covers "it underperformed", not "it broke".

## 1. The Guarantee Landscape

One cross-walk row per metric family, showing which instrument covers it on each side, so gaps and asymmetries are visible before the detail tables. A common asymmetry to check for and state explicitly: the offtake side often guarantees **power** (a capacity test) with no energy-retention counterpart, while every retention/energy test sits on the vendor side, so degradation reaches the offtaker only through the power and RTE tests. Verify the energy gap **line by line, not by assumption**: an offtake test protocol may measure and report energy capacity every test while no guaranteed level, adjustment, LD, or default trigger references it anywhere (a duration-product can carry MWh only in its nameplate definition, with every quantified instrument MW-denominated), and a power metric averaged over the full-rate discharge plateau does not capture duration at all: degradation shortens the plateau without lowering the average. Record the verified answer as a flag either way: if energy truly carries no instrument, the offtaker's likely fallback is reclassifying missing MWh as partial *unavailability*, which closes only if the derate-measurement convention is agreed before it is needed.

| Metric family | Owed to the offtaker | Held from vendor, one-time (supply-agreement commissioning) | Held from vendor, ongoing (service agreement · warranty) |
|---|---|---|---|
| Availability (power) | PG-101 | (acceptance testing instead) | PG-201 |
| Availability (response) | PG-104 | | ⚠️ PG-206, usually absent |
| Capacity (power) | PG-102 | PG-204 | |
| Energy retention | | PG-204 | PG-202 · PG-205 |
| Efficiency (RTE) | PG-103 | PG-204 | PG-203 |
| Dispatch / delivery | PG-104 | | |

Where instruments share a test-protocol shape, record what differs: meter location (POI vs project meter), measurand (power vs energy), temperature treatment.

The PG rows throughout §2–§3 are **seed examples** for a full-toll + LTSA + supply-agreement structure; reshape or delete them to match the project's actual contract stack (merchant/PPA projects replace the offtake rows).

### 1.1 "Availability" is four different measures, and a contract usually guarantees one

Before filling any row, resolve **which** availability an instrument actually guarantees. The [OBE framework](/Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) defines four types that fail independently, and most agreements guarantee one of them while calling it "availability" without qualification. That ambiguity is where availability disputes come from.

| Type | Answers | What guaranteeing only this one leaves uncovered |
|---|---|---|
| **EA** Equipment | Is the BESS on? | Imbalance, SOC error, and every derate. A fully "available" plant that cannot deliver rated power |
| **OA** Operational | Did it follow dispatch? | Everything outside dispatch hours, which is most of the year |
| **PA** Power | Can it deliver rated power now? | Whether the instruction was actually followed; and if defined with `max(charge, discharge)`, every one-sided derate |
| **QA** Energy | Does it still hold contracted MWh? | Everything fast. It is a retention scalar, not an operating measure |

Record the answer explicitly in each row's Assessment Basis, and where an instrument's own term collides with a toolkit term (a service agreement using "Power Availability" for a `max()`-based measure, for example), flag it in the [glossary](/Definitions%28DEF%29/definitions.md) as a ⚠️ two-meaning term.

### 1.2 The reference position

**Use this when the contract is silent, still being negotiated, or defines a term without saying how to compute it.** These are the toolkit's default methodologies, drawn from the OBE framework. They are engineering positions, identical on every project, and they are what the owner should be shadowing and negotiating toward. They are **not** guaranteed levels: percentages, LD rates, caps, and evaluation periods come from the executed agreement and nowhere else.

| Metric family | Reference methodology | Reference basis |
|---|---|---|
| **Availability (power)** | `BESS_AP = ADP` at full SOC, `ACP` at empty, `min(ACP, ADP)` otherwise. Bus roll-up on the **weakest** string times connected strings, capped at contract power | Contracted capacity, POI-normalised with directional losses, 1 to 5 min intervals |
| **Availability (equipment)** | `min(contactor, BMS status)` at string, `min(Σ strings, Σ PCS modules)` at bus, summed to site plus excused MW. Data gap defaults to unavailable | Both installed and contracted denominators, capped at 100% |
| **Availability (operational)** | Shortfall `= max(0, |setpoint| − |actual|)`, ramp-widened dead-band, **directional** SOC exclusion | Revenue meter at POI against the dispatch instruction as received |
| **Capacity / energy retention** | `QA = min(Q_test / Q_guaranteed, 100%)` from the most recent controlled capacity test. Discharge taper attributed to imbalance, not degradation | Annual test at minimum; a scalar between tests |
| **Efficiency (RTE)** | SOC-matched window, with the raw figure reported beside it. Suppressed below one full cycle rather than printed | One declared boundary used for charge, discharge and throughput alike |
| **Dispatch / delivery** | Same construction as operational availability, including the directional SOC exclusion | Interval resolution matching settlement |

Three conventions worth carrying into every sheet regardless of family:

- **Excused capacity belongs in the denominator**, so excused time is neutral. Removing it from the numerator instead penalises the owner for outages the contract already forgave, and it is a drafting error worth catching on first read.
- **Losses are directional.** They reduce discharge and increase charge. One loss figure applied to both is wrong in one direction.
- **The owner must hold its own copy** of every signal a guarantee is computed from, at equal or better resolution. A provider-computed, provider-archived, untested number cannot be contested, and the capture cannot be created retrospectively.

#### Arguing the power availability position

The reference power availability above is the one to negotiate into a service agreement. Four arguments, in the order they usually come up:

- **It takes the minimum of charge and discharge**, so a one-sided derate is visible. The single most valuable property, and the one most often drafted away.
- **It is rail-aware**, so a full battery is not penalised for being unable to charge, nor an empty one for being unable to discharge. This removes the only legitimate objection to a plain minimum, and with it the usual justification for a `max()` rule.
- **It rolls up through the physical topology**, weakest string then bus then site, so the number reflects what the plant can sustain rather than an optimistic sum.
- **It asks the provider to instrument nothing new.** Every input (cell voltages, current limits, SOC, temperature derates, voltage limits, reactive-power priority) is already published by their own equipment.

Terms to settle in the same clause, each of which is worth more than the guaranteed percentage itself:

| Term | What to specify | Why |
|---|---|---|
| Interval | 1 to 5 minutes, and the minimum valid intervals per hour | Coarse intervals hide short derates; an undefined validity rule lets bad hours drop out of the denominator |
| Measurement boundary | POI, with directional loss adjustment | Losses reduce discharge and help charge. One loss figure for both is wrong in one direction |
| Capacity basis | Guaranteed power, and whether installed headroom may offset | Decides whether overbuild absorbs failures before the guarantee is touched |
| Data ownership | Owner-side capture of the same signals at equal or better resolution | Without it the annual number cannot be checked, and it cannot be created retrospectively |
| Validation | Controlled tests with forced imbalance or imposed PCS limits | Turns a provider estimate into evidence |

**The fleet-size effect** is worth understanding before relying on any site-level power guarantee: with many small units against a site-level number, unit-level failures barely move the ratio, and only site-level events (a site-controller fault, an MV system failure, a common-mode defect) realistically threaten it. A guarantee of that shape is largely a guarantee against site-level failure.

### 1.3 Cross-reference to the metrics tree

**The [metrics tree](/Metrics_Tree%28MT%29/metrics-tree.md) is the source of truth for what gets monitored; this matrix is the source of truth for what the contract says.** Every mechanism drawn on the tree must have a row here. The reverse does not hold: this matrix carries instruments that exist contractually but are not daily-monitored, and that asymmetry is deliberate.

| Metrics tree node | Row here |
|---|---|
| Availability LD | PG-101 |
| Dispatch adjustment | PG-104 |
| Capacity and RTE adjustment | PG-102 · PG-103 (the tree combines them into one node; keep them separate here, since they are separate mechanisms with separate tests) |
| Power availability guarantee | PG-201 |
| Capacity guarantee | PG-202 |
| Central outage log | not a guarantee. It is the shared event record every row's excused treatment reads, and it is judged **once per contract**; see §5 |
| OBE and its four measures | not guarantees. They are the measurement layer in §1.2 that the rows above are computed from |

**Rows deliberately not on the tree**, with the reason, so their absence reads as a decision rather than an oversight:

| Row | Why it is off the tree |
|---|---|
| PG-203 vendor RTE | A real claim, but the tree scopes the vendor side to power availability and capacity for readability. Add a node if RTE recovery is material on the project |
| PG-301 O&M / BOP SLAs | Response-time and service obligations, not a performance measurement the platform computes |
| PG-204 commissioning acceptance | One-time, pre-operation. It sets the denominators the ongoing rows anchor to, then stops mattering |
| PG-205 retention warranty | A defect-claim path that happens to have a measured trigger; the tree already carries the measurement as the capacity guarantee |
| PG-206 response guarantee | Seeded here as a **gap row**. It has no tree node because it usually has no contract; the tree carries the underlying measure as Operational Availability under OBE |

When the tree changes, check this table first. A new node with no row here is an unmapped mechanism; a row whose tree node was deleted is a monitoring gap.

## 2. Owed to the Offtaker

The guarantees the owner gives under the offtake/tolling agreement (plus any interconnection response/ramp obligations).

**Levels, LDs and caps come from the executed agreement.** The Assessment Basis column is pre-filled with the §1.2 reference methodology, to be replaced by the contract's own definition once transcribed, or kept and cited if the contract is silent on method.

| ID | Guarantee | EIM Node | Guaranteed Level | Evaluation Period | Assessment Basis (telemetry / test / event log) | LD / Remedy (reference) | Cap | Calc Sheet |
|----|-----------|----------|------------------|-------------------|--------------------------------------------------|--------------------------|-----|-----------|
| PG-101 | Availability | | ❓ per agreement | ❓ typically annual | ❓ **which type?** Reference: PA per §1.2. Confirm whether the agreement counts logged outage hours or telemetry, since only the latter can be shadowed | | | `calc-offtake-availability.md` |
| PG-102 | Capacity (power test) | | ❓ per agreement | per test | metered acceptance test at POI; confirm whether duration is tested or only power | | | `calc-offtake-capacity.md` |
| PG-103 | Efficiency (RTE) | | ❓ per agreement | per test | metered test, SOC-matched window, boundary declared | | | `calc-offtake-rte.md` |
| PG-104 | Dispatch compliance | | ❓ per agreement | interval / event | OA per §1.2, meter vs setpoint with ramp-widened dead-band and directional SOC exclusion | | | `calc-offtake-dispatch.md` |

## 3. Held from Vendors

Every guarantee the owner holds, grouped by instrument. Record the guaranteed denominators (e.g. guaranteed power/energy capacity) once resolved from the supply agreement; every vendor instrument usually anchors to them.

### 3.1 Supply agreement: commissioning acceptance (one-time)

| ID | Guarantee | EIM Node | Guaranteed Level | Evaluation Period | Assessment Basis | LD / Remedy (reference) | Cap | Calc Sheet |
|----|-----------|----------|------------------|-------------------|-------------------|--------------------------|-----|-----------|
| PG-204 | Commissioning performance (power / energy / RTE) | | | one-time at commissioning | metered acceptance test | | | |

Capture: repeat-until-pass mechanics, per-guarantee acceptance rules (a guarantee once met may stay met regardless of the others), precondition waivers, buydown options (whose option, at what rate, against which floating exposure), and how the results anchor the offtake side's contract capacity and the ongoing retention baselines.

### 3.2 OEM service agreement: operating guarantees

| ID | Guarantee | EIM Node | Guaranteed Level | Evaluation Period | Assessment Basis | LD / Remedy (reference) | Cap | Calc Sheet |
|----|-----------|----------|------------------|-------------------|-------------------|--------------------------|-----|-----------|
| PG-201 | Availability | | ❓ per agreement | ❓ typically annual | ⚠️ telemetry, often vendor self-assessed. **Negotiate toward PA per §1.2**; refuse `max(charge, discharge)`, which cannot see a one-sided derate | | | `calc-service-power-availability.md` |
| PG-202 | Energy retention / capacity | | ❓ per agreement | per test | QA per §1.2, controlled capacity test | | | `calc-service-energy-retention.md` |
| PG-203 | Efficiency (RTE) | | ❓ per agreement | per test | metered test, SOC-matched, boundary declared | | | `calc-service-rte.md` |
| PG-206 | Operational availability (response) | | ❓ **usually absent** | interval | OA per §1.2, attributed to the provider's boundary | | | `calc-service-operational-availability.md` |

Capture: shared LD caps, throughput conditioning and row-shift rules, guarantee-termination triggers (cumulative throughput, relocation, service termination), and who computes each number (self-assessed guarantees need a shadow calculation).

**PG-206 is seeded deliberately as a gap row.** The service agreement typically covers the site controller, so the provider owns the plant's ability to act on an instruction as well as its capability to deliver power, yet almost no agreement measures the former. Leave the row in with its level marked absent: it makes the uncovered exposure visible in the matrix, and it is the specification to table at the next amendment. Shadow it from day one either way, because the evidence cannot be reconstructed later.

### 3.3 Defect-warranty instruments with a measured trigger

| ID | Guarantee | EIM Node | Guaranteed Level | Evaluation Period | Assessment Basis | LD / Remedy (reference) | Cap | Calc Sheet |
|----|-----------|----------|------------------|-------------------|-------------------|--------------------------|-----|-----------|
| PG-205 | Energy retention warranty (equipment warranty) | | | per claim | owner-run capacity test | repair/replace (defect path) | | |

Boundary note: the claim path and owner-side validity conditions live in the **Warranty Obligation Matrix**; a row sits here only when its trigger is a measured performance test. Where multiple retention instruments anchor to the same guaranteed energy value, add an endgame comparison (instrument | curve ends | level at end | remedy), since the floors and lifetimes usually differ.

### 3.4 O&M / BOP SLAs

| ID | Guarantee | EIM Node | Guaranteed Level | Evaluation Period | Assessment Basis | LD / Remedy (reference) | Cap | Calc Sheet |
|----|-----------|----------|------------------|-------------------|-------------------|--------------------------|-----|-----------|
| PG-301 | O&M / BOP SLAs (response, availability) | | | | | | | |

## 4. Calculation Sheets

The core of this document. **One standalone sheet per guarantee**, so each can be reviewed, implemented, and disputed independently. Each sheet must answer, completely:

1. **What is guaranteed**: metric, level, direction.
2. **Evaluation period and cadence**: annual average? per test? monthly? When does the clock start?
3. **Exact formula**: transcribed from the contract, with every defined term resolved.
4. **Measurement boundary and source**: POI meter? inverter terminals? controller estimate? Which physical/logical point, which system produces the number, and any loss adjustments between boundaries.
5. **All conditions and exclusions**: every event class that is excused, excluded, or adjusts numerator vs denominator; notice requirements; caps on exclusions; data-gap handling; temperature/voltage adjustment tables; test preconditions.
6. **Edge cases and traps**: deemed values, one-sided measurements, freeze/termination triggers, proration rules.
7. **Data and metrics required to shadow it**: the tags, event logs, and derived series Performance Engineering must maintain to reproduce the counterparty's number.
8. **Reporting and verification**: who computes it, what report, when, and the review/dispute window.
9. **LD/remedy (reference)**: recorded but secondary.
10. **Open items**: undefined terms, missing referenced documents.

Sheets live in the `calculations/` subfolder as `calc-{contract}-{metric}.md`.

**Start every sheet from the §1.2 reference position, then overwrite it with the contract.** Where the contract defines a method, the contract governs and the reference becomes the shadow calculation run beside it, with the delta reported. Where the contract is silent on method, the reference *is* the method: cite it in the sheet so the choice is visible and reviewable rather than buried in code. Either way the sheet states both numbers, because the gap between what the contract measures and what the plant did is the finding, and reconciling it away destroys the only evidence that matters.

## 5. Exclusions & Excuse Asymmetry

The money question when the owner sits between an offtake guarantee (owed) and supplier guarantees (held): **which outage/event causes are excused on one side but not the other**. Every row where the answer differs is retained owner risk.

**One event, judged once per contract.** All of these verdicts are rendered against the **same record**: the central outage log on the [metrics tree](/Metrics_Tree%28MT%29/metrics-tree.md), which sits outside both agreements because it belongs to neither and is read by both. Every grid outage card, scheduled maintenance window and material derate lands there once, GADS-aligned per the [Outage Tracker](/Data_Product%28DP%29/Outage_Tracker/outage-tracker.md), and then each contract rules on it independently. This table is where those rulings are compared, and every row where they differ is retained owner risk.

Fill the first three columns from the executed agreements. The **Expected pattern** column is pre-filled with what these classes usually do, as a checklist of what to go looking for; confirm or overwrite each one, and treat a row that comes back different from the pattern as a finding worth escalating rather than a clerical correction.

| Event / cause class | Treatment under offtake guarantee | Treatment under LTSA/OEM guarantee | Treatment under O&M SLA | Expected pattern, and why it bites |
|---------------------|-----------------------------------|-------------------------------------|--------------------------|--------------------|
| Supplier planned maintenance | | | | Excused both sides, but capped on the vendor side and notice-conditioned on the offtake side. Overflow past either cap usually becomes chargeable |
| BOP planned maintenance | | | | Excused offtake with notice; **excused for the vendor too**, since BOP sits outside their boundary. Owner carries it alone |
| BOP forced outage (transformer, switchgear, gen-tie) | | | | ⚠️ Counts fully against the owner offtake-side, excused vendor-side. One of the two largest retained exposures |
| Controls/SCADA/comms outage (incl. failed dispatch commands) | | | | ⚠️ The event that scores opposite ways: a command timeout is an owner-side cause to the vendor and a forced outage to the offtaker. The other largest exposure |
| BESS equipment fault | | | | The one class that generally counts against the vendor. Confirm it is not diluted by a `max()` availability definition |
| One-sided capability loss (charge-only or discharge-only) | | | | ⚠️ Invisible under a `max(charge, discharge)` definition, fully visible to the offtaker. Pure retained risk created by drafting, not by the plant |
| Missed setpoint / dispatch deviation | | | | Usually chargeable offtake-side with no vendor counterpart, because service agreements rarely measure response (see PG-206) |
| Grid outage / curtailment / voltage excursion | | | | Often excused both sides, but via different definitions. Vendor-side voltage-band adjustments can excuse more than the offtake FM clause does |
| Force majeure (compare definitions!) | | | | Never identical between contracts. Compare word by word; the gap is retained |
| Performance test hours | | | | Usually excused, and often outside any planned-outage cap. Confirm which party's requested tests qualify |
| Telemetry/data gaps | | | | ⚠️ Frequently no rule at all on the offtake side, while the vendor side drops low-validity hours from its denominator. Asymmetric in the vendor's favour |
| Low SOC / energy state | | | | Excusable only where the limit is in the **same direction** as the request. A non-directional rule forgives the failures that matter |
| Owner-caused delay (access, LOTO, approvals) | | | | Excused vendor-side, chargeable offtake-side. Controllable, so it belongs in the RACI and the SOPs |
| Throughput/cycling over-use | | | | Owner-side breach on both instruments; can shift retention rows or terminate the guarantee outright |

## 6. Shadow Calculation Requirements

Consolidated from the calc sheets: the event logs, telemetry series, and derived metrics the platform must maintain to independently reproduce every guaranteed number. Feed these into the Data Interface Register and the Metrics Tree.

> **This table is a data-requirements inventory, not a definition home.** A shadow calculation is an **owner metric**: its authoritative definition (formula, inputs, sources of truth, targets) lives in the Metrics Tree under an `MT_` code, one home per metric. This matrix states *what each guarantee requires captured*; the Metrics Tree defines *how the owner computes it*. The **Metric (ref)** column carries that pointer: fill it with the `MT_` code where the requirement is (or directly feeds) an owner metric, ❓ where a code will be assigned when the metric is built (never speculatively), and — for raw inputs, externals, and records that are not metrics.

**Pre-filled from the §1.2 reference methodology.** The signal list any BESS availability calculation needs is known in advance, so this table starts populated rather than blank. Add the project's own tag names and confirm each source; delete rows only when the corresponding guarantee genuinely does not exist. **Set this capture up before the first operating year closes**, because none of it can be reconstructed retrospectively, and without it no counterparty number can be contested.

| Requirement | Type (tag / event log / derived) | Serves Guarantee(s) | Metric (ref) | Resolution / Retention | Source System | Status |
|-------------|----------------------------------|----------------------|--------------|------------------------|---------------|--------|
| String DC contactor status | tag, per string | PG-101, PG-201 (EA) | ❓ | 1 to 5 min | BMS | |
| String BMS availability status | tag, per string | PG-101, PG-201 (EA) | ❓ | 1 to 5 min | BMS | |
| String and DC bus voltage | tag | EA signal quality control | ❓ | 1 to 5 min | BMS | |
| PCS module status and availability | tag, per module | PG-101, PG-201 (EA) | ❓ | 1 to 5 min | PCS | |
| PCS AC / module AC / DC bus voltages | tag | EA signal quality control | ❓ | 1 to 5 min | PCS | |
| String ACP and ADP | tag, per string or bus | PG-201, PG-206 (PA) | ❓ | 1 to 5 min | master BMS at bus level | |
| PCS module ACP and ADP | tag, per module | PG-201, PG-206 (PA) | ❓ | 1 to 5 min | PCS, POI-referred | |
| BESS SOC, in MWh and %, with declared basis | tag | PA, OA, QA | ❓ | 1 to 5 min | EMS / BMS | |
| Per-string and per-bus SOC | tag | imbalance attribution | ❓ | 1 to 5 min | BMS | |
| Dispatch setpoint as received | tag | PG-104, PG-206 (OA) | ❓ | settlement resolution | PPC / grid interface | |
| Actual power at the POI | tag | PG-104, PG-206 (OA) | ❓ | settlement resolution | revenue meter | |
| Setpoint acknowledgement and controller fault flags | tag / event | PG-206 (OA attribution) | ❓ | per event | site controller | |
| Capacity test results (`Q_test`) | test record | PG-102, PG-202, PG-205 (QA) | ❓ | per test, retained for life | test report | |
| Guaranteed capacity schedule (`Q_guaranteed`) | contract parameter | QA baseline | ❓ | per contract year | executed agreement | |
| Excused events: MW, start, end, cause, notice record | event log | every guarantee | ❓ | per event | [Outage Tracker](/Data_Product%28DP%29/Outage_Tracker/outage-tracker.md) | |
| POI normalisation constants (MVA vs MW, directional losses, C-rate) | static parameter | every MW-based guarantee | ❓ | on change | design documents | |
| Temperature, voltage and reactive-power derate signals | tag | PA derate attribution | ❓ | 1 to 5 min | PCS | |

## 7. LD & Claims Tracker (reference)

| # | Period | Guarantee | Reported Value | Shadow Value | Delta | LD Claimed / Owed | Status |
|---|--------|-----------|----------------|--------------|-------|--------------------|--------|
| | | | | | | | |

## 8. Calendar: Key Dates

| Date | Event (test window, report due, review/dispute deadline, guarantee step-down) | Guarantee | Action Owner |
|------|-------------------------------------------------------------------------------|-----------|--------------|
| | | | |

---
*Related: the [Warranty Obligation Matrix](../Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) holds defect-style warranties and the owner-side conditions that keep them valid. The availability methodology behind §1.2 is specified in the [Daily Performance Report](/Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) and sourced in [references](references/index.md).*
