---
type: Template
title: "MT_POM_002 — Auxiliary load"
description: "Owner metric: house loads as a share of throughput, and standby auxiliary power while idle. Feeds PG_103 and PG_203, and the capacity-test conditions."
---

# `MT_POM_002` — Auxiliary load

**Class:** monitoring · **Segment grain:** `POM` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** How much of the plant's throughput the plant **consumes on itself**: HVAC, BMS, controls, lighting. Reported as a share of throughput and as standby kW while idle. It depresses operating RTE and, if auxiliaries sit inside the test boundary, the tested capacity and RTE too. It is **not** a loss inside the conversion chain (that is inside RTE) and not a settlement quantity, though some tariffs bill it separately.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_POM_002` |
| **Formula** | `aux_share = Σ aux_energy / Σ throughput`, where `throughput = Σ energy_exported + Σ energy_imported` at the POM (state if export-only is preferred); `aux_standby_kw = mean aux power over intervals with `|active_power| < {{idle}}` kW`. **Gaps:** a missing aux-meter interval is `null` and the day's share is flagged; never derived from the POM meter for that interval unless the derived method is the declared primary. |
| **Units** | % of throughput; kW standby; MWh per month |
| **Measurement boundary** | the auxiliary meter (`Meter`, auxiliary, under `POM`); or, where none exists, derived as the POM gross minus the PCS net over the same interval, declared as the primary with its tolerance |
| **Inputs & sources of truth** | aux `energy_imported` from the **auxiliary meter** (wins where it exists); POM `energy_exported` / `energy_imported` from the **revenue meter**; `active_power` at the POM for the idle test; PCS-summed energy for the derived method only |
| **Secondary / cross-check source** | the derived method where a meter exists, and vice versa; `ambient_temperature` for the seasonality context |
| **Calculation interval** | daily; monthly |
| **Aggregation** | daily sums to monthly; standby kW as a daily mean over idle intervals |
| **Timezone & clock convention** | `{{TZ}}`, calendar |
| **Exclusions** | none |
| **Target / alert** | no contractual target. Alert when the monthly share exceeds `{{x}}`% or standby exceeds `{{y}}` kW, seasonally adjusted. Review monthly |
| **Related PG** | feeds `PG_103` and `PG_203`; feeds the test conditions of `PG_102` and `PG_202` |
| **Owner of the calculation** | `[PE]`, materialised daily |
| **Reporting chain** | Monthly Performance Report, dashboards; the O&M review (HVAC setpoints are the usual lever) |
| **Reconciliation / plausibility checks** | aux energy `≥ 0` always; aux `≤ {{z}}`% of throughput (above it, a metering error is more likely than a real value); HVAC seasonality visible; where a tariff bills auxiliaries, the metered figure reconciles to the invoice |
| **Known discrepancies** | The derived method carries the PCS-vs-meter tolerance and reads noisy on low-throughput days. Where the tested RTE excludes auxiliaries and the operating RTE includes them, part of the gap in `MT_POM_001` is this metric |

## Easy to get wrong

Reporting a share on low-throughput days without the standby kW beside it (the share explodes, the load did not). Not stating whether auxiliaries sit inside the RTE and capacity-test boundaries. Deriving from the POM meter without declaring the tolerance.
