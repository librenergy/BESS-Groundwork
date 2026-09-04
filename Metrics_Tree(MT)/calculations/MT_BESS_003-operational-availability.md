---
type: Template
title: "MT_BESS_003 — Operational Availability (OA)"
description: "Owner metric: did the plant follow the setpoint? Revenue meter against the instruction actually sent, with a ramp-widened dead-band and a directional SOC exclusion. Evidences PG_104."
---

# `MT_BESS_003` — Operational Availability (OA)

**Class:** engineering · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** Whether the plant **delivered what it was told to**, measured as a revenue-grade meter against the setpoint actually received. The most defensible number in the set and the one with the least coverage: it exists only while the plant is being dispatched and says nothing when the setpoint sits below nameplate or the plant is idle. It is **not** capability (`MT_BESS_004`), not on/off (`MT_BESS_002`), and not the contractual dispatch compliance: `PG_104` (and `PG_206` where a service agreement carries one) apply their own tolerances and excusals. It evidences them.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_003` |
| **Formula** | see Method. `Shortfall(i) = max(0, |setpoint| − |actual|)`; dead-band `{{±x}}` MW widened in proportion to ramp rate; where `Shortfall` exceeds the dead-band and the directional SOC exclusion does not apply, `OA_MW(i) = |actual(i)|`, else deemed `|setpoint(i)|`. `OA_%(i) = OA_MW(i) / |setpoint(i)|` (and against contracted MW as the secondary). **Defined only for intervals with `|setpoint| ≥ {{threshold}}` MW**; elsewhere the interval is `null`, never 100%. **Gaps:** a missing meter sample voids the interval (`null`); a missing setpoint sample with a meter sample present is an **investigation** (the plant was possibly dispatched with no record), logged, not zeroed. |
| **Units** | MW; % of setpoint (primary), % of contracted power (secondary) |
| **Measurement boundary** | the revenue meter at the POM, on the same side of the transformer as the setpoint is defined; state it. Where the control loop closes on a different meter than settlement reads, the settlement meter wins for the number and the loop meter is the cross-check |
| **Inputs & sources of truth** | `power_setpoint` as **received at the plant controller** (the PPC's logged instruction wins over the offtaker's sent record and over the OEM site controller's mirror); `active_power` from the **revenue meter** (wins over PCS sum and EMS estimate); `soc_percent_installed` at BESS grain (**master BMS**) for the rail test; `contracted_power`; dead-band, ramp and response parameters from [Daily Report §9.4](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) |
| **Secondary / cross-check source** | PCS-summed `active_power` for meter validation (tolerance `{{x}}`%); the offtaker's dispatch log for setpoint-stream integrity |
| **Calculation interval** | `{{1 to 5}}` min, or the settlement interval if coarser; the setpoint sample rate must be at least the calculation rate |
| **Aggregation** | interval → dispatched-hours mean (only defined intervals) → daily → month; always publish the **coverage** (dispatched intervals ÷ all intervals) beside the number |
| **Timezone & clock convention** | `{{TZ}}`, calendar; setpoint and meter timestamps aligned to the same clock and the same interval convention (start- or end-stamped, stated) |
| **Exclusions** | the directional SOC exclusion only (see Method); it is part of the formula, not an excuse event |
| **Target / alert** | no contractual target. Alert when the dispatched-hours mean drops below `{{x}}`% on a day, and on any interval with shortfall above `{{y}}` MW outside the SOC exclusion. Review daily |
| **Related PG** | evidences `PG_104` (and `PG_206` where it exists) |
| **Owner of the calculation** | `[PE]`, materialised in the platform; the setpoint capture is the precondition and must exist from COD |
| **Reporting chain** | Daily Performance Report §2.2 and §4.2, `MT_BESS_001`, dashboards, Monthly Performance Report |
| **Reconciliation / plausibility checks** | over-delivery is never a credit; response-time compliance (`{{n}}` s to acknowledge) tracked as its own count; setpoint stream validated against the offtaker's log (missing or duplicated instructions logged as events); meter vs PCS sum within tolerance |
| **Known discrepancies** | Reads **below** `PG_104` where the offtake's dead-band is wider or its SOC exclusion is non-directional, and above it where the offtake counts undefined intervals as non-compliant. Coverage is structurally low; do not extrapolate it to calendar time |

## Method

**Shortfall.** Under-delivery only. Over-delivery is always zero, never a credit.

```
Shortfall(i) = max( 0, |setpoint(i)| − |actual(i)| )
```

**Dead-band.** A configurable MW tolerance below which shortfall is not penalised, widened in proportion to ramp rate so the plant is not faulted for lag it was ordered into:

```
ramp_rate(i) = rolling max over the previous 2 intervals of min( |Δsetpoint|, |Δactual| ),
               expressed as % of contract MW per second
dead_band(i) = dead_band_stable + f(ramp_rate(i))     -- up to dead_band_max when fully ramped
```

Taking the **minimum** of the setpoint change and the actual change widens the band only for ramps the plant genuinely attempted, not for a step command it ignored.

**From shortfall to availability.** For every defined interval where `Shortfall > dead_band` and SOC is not the cause, the actual output *is* the available power:

```
OA_MW(i) = |actual(i)|        if Shortfall(i) > dead_band(i) and not SOC_excused(i)
         = |setpoint(i)|      otherwise (deemed)
```

**The SOC exclusion is directional.** A shortfall is excused only when the battery has hit a physical limit **in the same direction as the request**: SOC at 100% and the setpoint is charging, or SOC at 0% and the setpoint is discharging. If the directions oppose (an empty battery asked to charge, a full one asked to discharge) the shortfall counts **fully**: nothing physical prevented it.

## Easy to get wrong

A non-directional SOC exclusion, which forgives exactly the failures that matter. Widening the dead-band on `|Δsetpoint|` alone, which forgives ignored steps. Deeming undefined intervals 100% and quoting a calendar-time figure. Reading the setpoint from the offtaker's sent log instead of the plant's received log: the gap between the two is a comms finding, not an availability one.

## References

[M01 E03 Operational Availability Deep Dive](../../BESS_Experts/m01-e03-operational-availability-deep-dive.pdf); implemented in [Daily Performance Report §2.2](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md).
