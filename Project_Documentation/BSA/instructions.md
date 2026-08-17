---
type: Guide
title: Battery Supply Agreement Review Instructions
description: How to review a battery/system supply agreement step by step - scope of supply, design ratings, warranties and defect definitions, commissioning, controls and response times, and the installed-vs-guaranteed reconciliation.
---

# Battery Supply Agreement Review Instructions

How to review the documents in this folder — the battery or system supply agreement (SPA/BSA), its exhibits, and amendments. The mechanical workflow lives in the [`project-document-review`](/.agents/skills/project-document-review/SKILL.md) skill; this file is the domain-specific method.

**The lens: this is a *supply* contract — the spine is sizing, quantity, and ratings.** Where the service agreement asks "what is promised over 20 years?" and the offtake asks "what do we owe?", the supply agreement asks **what exactly was bought, at what rating, proven how, and warranted against what**. It is also the document the *other* contracts stand on: it typically defines the guaranteed capacities the service agreement's formulas reference, the defect warranty the service agreement carves around, and the completion dates that start every downstream clock.

**Scope question first:** what is the supplied unit? An **AC block** (integrated DC storage + PCS, one product) or **DC blocks with PCS supplied separately**? This decides whether a separate PCS supply agreement exists or should — record the answer prominently, and if PCS is separate, expect a sibling folder for it.

**Output = companion files**, each referenced from `summary.md` and the folder `index.md`:

| File | Holds |
|---|---|
| `definitions.md` | Every defined term, one line each, source clause, usage map — **the defect definitions especially** |
| `design.md` | Scope of supply, quantity, unit and plant ratings, configuration, installed vs guaranteed capacity |
| `guarantees.md` | Every warranty and guarantee: defect (with levels and serial-defect mechanics), capacity, efficiency, and the rest |
| `commissioning.md` | The commissioning sequence, tests, acceptance criteria, and the dates that start downstream clocks |
| `telemetry.md` | SCADA/controls interfaces, control features, points, and the **response-time review** |
| `summary.md` | Key facts + reporting, personnel, communication protocols, costs — pointers to the files above |
| `red-flags.md` | Features most likely to hurt the owner: clause as written (cited), why it bites, mitigation |

Keep everything **short, cited, and human-readable**. Use `pdftotext -layout` (tables jumble without it), render pages for surprising values, record typos as written.

## Step 1 — Exhibit inventory

The exhibit list is the map — supply agreements bury the ratings, the warranty, and the test protocols in exhibits. Record every exhibit and sub-exhibit; flag `[Reserved]` slots, exhibits referenced but not attached, and note which carry the performance content: **equipment specification, scope of supply / division of responsibility, performance guarantees, commissioning and test protocols, the warranty (often a separately named document incorporated by reference), spares**.

⚠️ **Scope discipline: purchase price, payment milestones, credit support and delivery logistics are deliberately out of scope for the companions** — this is a performance-engineering repo. Record only the **LD/adjustment/buydown economics** (rates, caps, and what a 1% or one-day miss costs) and the dates that start performance clocks; everything else stays in the contract with at most a one-line pointer.

## Step 2 — Definitions → `definitions.md`

Review and summarize **all defined terms** before reading anything else — one line each with source clause, completeness-checked (`grep '"X" means'` across all operative exhibits), acronym keys for the load-bearing ones, ❓ for terms defined in documents not held, ⚠️ for collisions with sibling contracts.

Two clusters deserve special care in a supply agreement:

- **The capacity vocabulary** — installed / nameplate / rated / usable / guaranteed / contract capacity are distinct terms here, and downstream contracts reference *these definitions*. Get each one exactly.
- **The defect vocabulary** — **what is a "Defect"?** Capture the definition verbatim, every **defect level or classification** (critical / major / minor, or equivalent), and the **"Serial Defect"** definition: the threshold (what % of units, failing how, within what window), what it triggers (fleet-wide remediation vs unit-by-unit repair), and who pays for the campaign. Serial-defect mechanics are the single most valuable clause in the document when a fleet-wide fault emerges.

## Step 3 — Design review → `design.md`

The sizing and quantity pass:

- **Quantity** — units, and units per collection group (e.g. per MV transformer). Cross-check against the design basis and SCADA one-lines; a mismatch is a finding.
- **Rated power** — per unit and plant-aggregate, charge and discharge, at what conditions (temperature, voltage) and at what boundary (unit terminals / MV / POI).
- **Rated energy** — per unit and aggregate, BOL, at what C-rate/duration and measurement point.
- **Configuration** — AC block vs DC-block+PCS (the scope question above), voltage class, collection ratio.
- **Installed vs guaranteed capacity** — the load-bearing comparison. The guaranteed figure is often below the installed one; the delta is the degradation buffer (and the overbuild that masks early fade). **Tabulate both, and reconcile against `project_info.md`** — the project's capacity block must carry the right numbers with the right names, or every downstream matrix inherits the confusion.
- **Augmentation provisions** — rights, obligations, space/electrical provisions for future units, or their verified absence.
- Delivery schedule and the dates that gate everything else.

## Step 4 — Warranties and guarantees → `guarantees.md`

Everything warranted or guaranteed, one section per instrument:

1. **Defect warranty** — scope, period and what starts it, defect levels and the remedy per level, exclusions **with carve-backs**, the owner-side conditions that keep it valid (operating envelope, maintenance, firmware), and the **serial-defect** threshold/trigger/remedy/cost allocation from Step 2.
2. **Capacity warranty / retention** — the year-by-year table in full, its measurement basis, the anchor value (this document usually *is* the anchor other contracts reference), test rights and cadence.
3. **Efficiency (RTE) warranty** — table, boundary, aux treatment, temperature adjustments.
4. **Any other guarantee** — availability during the warranty term, response-time or functional guarantees, delivery LDs.
5. For each: the seven questions (level, formula, measurement basis, evaluation period, LD/remedy and trigger, cure right, interactions) and **the split against the service agreement** — which instrument covers what, where they overlap, and what neither covers.

## Step 5 — Commissioning review → `commissioning.md`

How the system gets from delivered to accepted:

- The **sequence** (mechanical completion → energization → commissioning → capacity/performance tests → substantial completion / final acceptance), with the acceptance criteria and cure paths at each gate.
- The **test protocols** — what is measured, at what boundary, pass thresholds, who witnesses, who signs.
- **The dates that start downstream clocks.** Supply-agreement completion milestones typically start the service agreement's term, the warranty periods, and the guarantee evaluation calendar. Name each date and every clock it starts — this table is the document's most-referenced output.
- Punch-list mechanics, deemed acceptance, and what operating the system before acceptance does to the warranty.

## Step 6 — SCADA, telemetry, and controls → `telemetry.md`

- **Interfaces** — protocols, points, update rates, who integrates with whom; what the supplier's controller exposes vs withholds.
- **Control features** — operating modes, setpoint interfaces, grid-support functions (frequency response, voltage/VAR control, ride-through), and which are configurable vs fixed.
- **The response-time review** — a full pass on every latency and dynamics number in the document: command-to-response times (**milliseconds and seconds — record the units exactly**, ms-vs-s confusion is a classic drafting defect), **ramp rates** (MW/s and MW/min, up and down), **response gain/droop** settings and their adjustability, settling times, and reactive-vs-active power response. Cross-check each against what the offtake and interconnection agreements *require* — a supply spec slower than an offtake obligation is unbacked exposure.
- Capability curves: reactive power capability across the voltage/temperature envelope, active power at temperature.

## Step 7 — Installed vs guaranteed reconciliation (propagation)

Close the loop from Step 3: update `project_info.md`'s capacity block with the reconciled installed/guaranteed figures under their exact defined names, hand the guarantee content to the `performance-guarantee-matrix` and `warranty-obligation-matrix` skills, and check the anchor: every sibling-contract formula that references a capacity defined *here* should now resolve — list the ones that do and any that still don't.

## Step 8 — Keyword scan (verification pass)

The standard sets — obligation (*shall, must, responsible for, at its cost*), communication (*notify, notice, submit, consent, deemed*), money (*pay, fee, liquidated damages, invoice, cap, refund*), time (*within, days, hours, promptly*) — **plus the technical set this domain demands**: *temperature, voltage, current, reactive, active power, frequency, ramp, response, Hz, derat*. Every hit captured in a companion or consciously excluded; record the scan in the folder `log.md`. Follow with the **definitions cross-reference** (every use of each load-bearing term, "used in" notes).

## Step 8b — Red flags → `red-flags.md`

Anything structurally against the owner: a defect definition narrower than its name, serial-defect thresholds set unreachably high, warranty conditions ordinary operation will violate, capacity guaranteed at a boundary nobody meters, commissioning acceptance that starts clocks before performance is proven, response-time specs that undercut offtake obligations. As written / why it bites / mitigation.

## Step 9 — Close out

Missing referenced documents (the equipment warranty and the specification are the usual suspects); both indexes; folder `log.md`; `todo.md`; the definitions **roll-up into the master glossary in the same session**; propagation per Step 7.

## Review checklist

- [ ] Text extracted with `-layout`; surprising values page-verified; typos recorded as written
- [ ] Exhibit inventory; reserved and unattached exhibits flagged
- [ ] All defined terms extracted and completeness-checked; capacity and defect vocabularies verbatim
- [ ] **Scope of supply settled: AC block vs DC block + separate PCS**
- [ ] Quantity, rated power, rated energy — per unit and aggregate, with conditions and boundary
- [ ] **Installed vs guaranteed capacity tabulated and reconciled against `project_info.md`**
- [ ] Defect warranty with levels; **serial-defect threshold, trigger, remedy, cost allocation**
- [ ] Capacity and RTE warranties with full tables and anchors
- [ ] Commissioning sequence, tests, acceptance criteria; **every completion date mapped to the clocks it starts**
- [ ] SCADA/controls interfaces and features; response-time review (ms/s units, ramp rates, gain/droop) cross-checked against offtake and interconnection requirements
- [ ] Keyword scan incl. the technical set; definitions cross-reference
- [ ] Red flags written; glossary rolled up; guarantee content handed to the matrices; missing documents listed
