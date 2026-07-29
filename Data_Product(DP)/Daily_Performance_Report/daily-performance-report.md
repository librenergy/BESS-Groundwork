---
type: Template
title: Daily Performance Report
description: "Daily BESS performance report: availability under each contractual definition, energy and efficiency, data quality, commercial events, and the parameter block that makes the numbers reproducible."
---

# {{SITE_NAME}} — Daily Performance Report

**{{DAY_START}} – {{DAY_END}} ({{N}} day) · {{TZ_ABBR}}**
`{{SITE_CODE}}` · {{CONTRACT_MW}} MW Contract · {{INSTALLED_MW}} MW Installed · {{PCS_COUNT}} PCS

> **Template.** Structure modelled on a production daily report generator (HTML → PDF). Placeholders in `{{...}}`; every table shows its rows with no data. Delivery formats: [HTML skeleton](daily-performance-report-template.html) for the generator, this file for the specification and for hand-assembled issues.

## How to read this report

State the conventions in the report itself, every issue — a daily availability number is meaningless without them:

- **Excused treatment.** Primary values are **With Excused @ 100%** (excused minutes credited at contract MW). The italic line under each value shows **Without Excused** (no credit). Say which contract defines "excused".
- **Depth weighting.** Where shown, depth-weighted values forgive shallow-SOC operation on a declared curve (see [Report Parameters](#report-parameters)). Raw values apply no forgiveness.
- **Combined** is the **min of Equipment (Contract), Power, and Operational** — it deliberately **excludes** Equipment (Installed), because installed capacity above contract capacity is not a contractual obligation.

## Key Performance Indicators

*Availability values are With Excused @ 100%; italic caption shows Without Excused.*

| | | | |
|---|---|---|---|
| **Combined Raw**<br>`{{x}}%` · `{{MW}}` MW | **Combined (Depth-Weighted)**<br>`{{x}}%` · `{{MW}}` MW<br>*Without Excused: `{{x}}%` · `{{MW}}` MW* | **Discharged**<br>`{{MWh}}` MWh | **RTE (SOC Match)**<br>`{{x}}%` / N/A<br>*`{{reason if N/A}}`* |
| **Equip. (Installed)**<br>`{{x}}%` · `{{MW}}` MW<br>*Without Excused: `{{x}}%` · `{{MW}}` MW* | **Equip. (Contract)**<br>`{{x}}%` · `{{MW}}` MW<br>*Without Excused: `{{x}}%` · `{{MW}}` MW* | **Total Cycles**<br>`{{n}}` EFC | **Cycles / Day**<br>`{{n}}` EFC/day |
| **Avg SOC**<br>`{{x}}%` | | | |

> When RTE cannot be computed, say why rather than showing a blank — e.g. *"Throughput `{{x}}`% < 100% (under one cycle)"*. A silent N/A gets read as a failure.

## Availability Summary

*Primary values are With Excused @ 100% (excused minutes credited at contract MW). The italic line shows Without Excused. Combined excludes Equipment (Installed); it is the min of Equipment (Contract), Power, and Operational.*

| Metric | Raw | Depth-Weighted |
|---|---|---|
| **Equipment (Installed)**<br>*Without Excused* | `{{x}}%` · `{{MW}}` MW | — |
| **Equipment (Contract)**<br>*Without Excused* | `{{x}}%` · `{{MW}}` MW | — |
| **Power**<br>*Without Excused* | `{{x}}%` · `{{MW}}` MW | `{{x}}%` · `{{MW}}` MW |
| **Operational**<br>*Without Excused* | `{{x}}%` · `{{MW}}` MW | `{{x}}%` · `{{MW}}` MW |
| **Combined (min of 3)**<br>*Without Excused* | `{{x}}%` · `{{MW}}` MW | `{{x}}%` · `{{MW}}` MW |

What each definition measures — adapt the wording to the project's contracts, but keep the four separate:

| Definition | Measures | Typical contractual home |
|---|---|---|
| Equipment (Installed) | Equipment up against **installed** capacity | Internal / OEM view |
| Equipment (Contract) | Equipment up against **contract** capacity | OEM availability guarantee |
| Power | Deliverable power against contract capacity | Offtake availability guarantee |
| Operational | Setpoint tracking within tolerance | Dispatch compliance |
| Combined | min of Contract, Power, Operational | Headline number |

Equipment rows carry no depth-weighted value — depth weighting applies to power delivery, not to equipment state.

## Charts

Each chart is a full-day time series at the report's interval resolution. In the HTML/PDF generator these are embedded images; in a Markdown issue, link or embed them.

1. **Equipment Availability — Installed** — `{{chart}}`
2. **Equipment Availability by PCS** — `{{chart}}` (per-unit heatmap or small multiples; this is what turns a headline number into a work order)
3. **Operational Availability — Setpoint Tracking** — `{{chart}}` (setpoint vs actual with the tolerance band drawn)
4. **Power Availability** — `{{chart}}`
5. **Combined Availability** — `{{chart}}`

## Energy Summary

| Metric | Value |
|---|---|
| Total Charge | `{{MWh}}` MWh |
| Total Discharge | `{{MWh}}` MWh |
| RTE (SOC Matched) | `{{x}}%` |
| RTE (Raw) | `{{x}}%` |
| Equivalent Cycles | `{{n}}` |
| Total Minutes | `{{n}}` |
| Excused Minutes | `{{n}}` |

Report **both RTE figures** when the day is not SOC-neutral. SOC-matched trims the window to equal start/end SOC; raw does not. The two diverge by a large margin on a partial-cycle day, and quoting only one invites an argument.

## Data Quality

| Indicator | Value |
|---|---|
| Data Completeness (excl. excused) | `{{x}}%` (`{{n}}` / `{{n}}` min) |
| PCS with Data Gaps (<95%) | `{{list with per-unit % — or None}}` |
| RTE Boundary Effect | Raw `{{x}}%` vs SOC-Matched `{{x}}%` (delta `{{n}}`pp) |

A completeness figure at the site level hides per-unit gaps. List the units below threshold by name, with their percentage — a report claiming 100% site completeness while two thirds of the PCS fleet sits at 80% is misleading.

## Commercial Events ({{COUNT}})

| Event | Start ({{TZ_ABBR}}) | End ({{TZ_ABBR}}) | Duration | MW Down | Excused {{CONTRACT}} |
|---|---|---|---|---|---|
| `{{outage/derate reference and description}}` | `{{YYYY-MM-DD HH:MM}}` | `{{YYYY-MM-DD HH:MM}}` | `{{n}}`h | `{{n}}` MW | Yes / No |

Include events that **started before the reporting day** but overlap it. Put the counterparty's own outage/ticket reference in the event name so the row can be reconciled against their system. The excused column is a **contractual verdict**, not an observation — it must agree with the event ledger (BESS GADS) and the guarantee matrix.

## Report Parameters

Everything needed to reproduce the numbers. Without this block the report is an assertion.

### Site & Contract

| | |
|---|---|
| Contracted Power | `{{MW}}` MW |
| Installed Power | `{{MW}}` MW |
| Contracted Energy | `{{MWh}}` MWh |
| Discharge MWh at Contract Power | `{{MWh}}` MWh |
| PCS Count | `{{n}}` |
| Battery Count | `{{n}}` |
| Last Tested RTE | `{{x}}%` |
| COD Date | `{{YYYY-MM-DD}}` |
| Timezone | `{{IANA zone}}` |

### Operational Availability

| | |
|---|---|
| Stable Tolerance | `{{MW}}` MW (`{{x}}`% nameplate) — at ramp ≤ `{{x}}`%/sec |
| Max Tolerance | `{{x}}`% nameplate at ≥ `{{x}}`%/sec ramp |
| Ramp Window | `{{n}}` back + `{{n}}` forward intervals (centered rolling max) |

### Depth-Weighted Availability

| | |
|---|---|
| Forgiveness Threshold | `{{x}}`% SOC (discharge below `{{x}}`%, charge above `{{x}}`%) |
| Curve Exponent | `{{n}}` (weight = (SOC / threshold)^`{{n}}`) |

---

*Generated `{{TIMESTAMP}}` {{TZ_ABBR}} · {{GENERATOR}} v`{{VERSION}}` · {{SITE_NAME}} (`{{SITE_CODE}}`)*

## Conventions to pin down before the first issue

- [ ] **Measurement boundary per metric** — POI/POM revenue meter, plant controller, or OEM controller. RTE = discharge ÷ charge, so the boundary moves it by the transformer and auxiliary losses.
- [ ] **Which availability definitions the contracts actually require**, and their exact names. Four are shown here; a project may need fewer or more, but each contract's definition gets its own row — never one blended "availability".
- [ ] **What counts as excused**, under which contract, and who adjudicates. Report with and without excused credit every day; the two numbers are what a dispute turns on.
- [ ] **Cycle definition (EFC)** — energy-based or count-based, charge or discharge side, at which boundary — matching the OEM guarantee.
- [ ] **Day boundary and timezone**, verified across daylight-saving transitions, and rolling up cleanly into contract-year totals.
- [ ] **Setpoint tolerance bands and the ramp window** for operational availability. These are judgement calls that change the result; publish them in the parameter block.
- [ ] **Depth-weighting curve** — threshold and exponent — or state explicitly that no forgiveness is applied.
- [ ] **Provisional vs final, and restatement.** Daily numbers get corrected by late meter data and reclassified outages; carry a version and a supersedes marker.
- [ ] **Emit from the authoritative calculations** in the Performance Guarantee Matrix and Metrics & KPIs — never recompute inside the report. A daily figure that disagrees with the monthly guarantee calculation is worse than no figure.

A machine-readable daily feed to a counterparty is a **different product** built from the same numbers — scope it separately with the `data-product` skill, because the counterparty usually supplies the schema.
