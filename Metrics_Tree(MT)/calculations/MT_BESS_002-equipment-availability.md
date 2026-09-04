---
type: Template
title: "MT_BESS_002 — Equipment Availability (EA)"
description: "Owner metric: is the equipment on? Built bottom-up from contactor, BMS and PCS status signals, min at the string, min of the two sides at the DC bus, summed to site with an excused term. Evidences PG_101 and PG_201."
---

# `MT_BESS_002` — Equipment Availability (EA)

**Class:** engineering · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** Whether the equipment is **in service**, from status signals only: contactor closed, BMS reporting available, PCS module ready. Full calendar coverage, high accuracy, and blind to everything that is not on/off: imbalance, SOC error and derates are invisible to it (those are `MT_BESS_004`). It is **not** delivered energy, not setpoint-following (`MT_BESS_003`), and not the contractual availability: `PG_101` and `PG_201` count outage hours on their own rules. It evidences both.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_002` |
| **Formula** | see Method. String: `min(connected, bms_availability)`. PCS module: `min(module_status, module_availability)`. Bus: `min(Σ string MW, Σ PCS module MW)`, **0 on a data gap**. BESS: `Σ_bus + Excused_MW(i)`. Published as `EA_MW(i)`, `EA_installed_%(i)`, `EA_contracted_%(i)`. **Gaps:** an interval with no status sample for a bus is 0 for that bus, never last-known; genuine telemetry loss during known-good availability is credited back through `Excused_MW`. |
| **Units** | MW, and % on installed and contracted bases |
| **Measurement boundary** | POI-normalised: every string and module capacity converted to POI MW before summing ([Daily Report §9.2](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md): apparent-power rating, directional losses, sustainable C-rate) |
| **Inputs & sources of truth** | `connected` per `Bat_Str` (**BMS**, the contactor's own status word wins over any EMS mirror); BMS availability status per string (**BMS**); `status_code` / availability per `PCS_Mod` (**PCS**, over the site controller's copy); `voltage` per string and bus, PCS AC/DC voltages (**quality control only**, never an input); `Excused_MW(i)` (**[Outage Tracker](../../Data_Product%28DP%29/Outage_Tracker/outage-tracker.md)**, the one event record); `nominal_power` per segment and `contracted_power` (dimension data per the ontology) |
| **Secondary / cross-check source** | Site-controller availability summary, for divergence only. Voltage deltas: with `connected = 1`, `abs(string voltage − bus voltage)` under about `{{1}}` V; `abs(PCS AC voltage − module AC voltage)` and `abs(DC bus voltage − module DC voltage)` within `{{x}}` V |
| **Calculation interval** | `{{1 to 5}}` min; minimum valid intervals per hour `{{n}}` |
| **Aggregation** | string → bus (`min` of the two sides) → BESS (`Σ` + excused) per interval; period value `Σ_i EA_MW(i) / N`; per-unit hourly heatmap for diagnosis |
| **Timezone & clock convention** | `{{TZ}}`, calendar engineering series |
| **Exclusions** | none: the excused term **adds back** MW for scheduled maintenance, grid outages and data loss during known-good availability, per the Outage Tracker's verdict; it never removes intervals from `N` |
| **Target / alert** | no contractual target. Alert when `EA_installed_% > 100%` (denominator error), on any bus at 0 for more than `{{n}}` intervals without an event, and on a voltage-delta QC failure. Review daily |
| **Related PG** | evidences `PG_101` and `PG_201` |
| **Owner of the calculation** | `[PE]`, materialised in the platform at string, bus and BESS grain |
| **Reporting chain** | Daily Performance Report §2.1 (time series, per-unit heatmap), `MT_BESS_001`, dashboards, Monthly Performance Report |
| **Reconciliation / plausibility checks** | installed basis never clipped (a value above 100% is alerted, not hidden); contracted basis capped at **200%** so the overbuild headroom stays visible; `Σ_bus ≤ Σ nominal_power`; every 0-interval on a bus either matches an Outage Tracker event or becomes one |
| **Known discrepancies** | Runs **above** `PG_201`'s availability where the vendor's definition includes derates, and **above or below** `PG_101` depending on which events the offtake excuses. A unit off for a reason the vendor is excused for still reads unavailable here; the excuse lives in the PG sheet |

## Method

**String.** Two signals, the weaker governs:

```
String_Avail = min( DC contactor status [open, closed], BMS availability status [0%, 100%] )
```

**PCS module.** Same pattern one layer up:

```
PCS_Module_Avail = min( module status [ready, run vs stopped, faulted, off], module availability [0%, 50%, 100%] )
```

**Bus.** A DC bus is a series chain, worth the weaker of its battery side and its conversion side:

```
Avail_Bus_MW(bus, i) = min( Σ Avail_String_MW(bus, string, i), Σ Avail_PCS_Module_MW(bus, i) )   if data is available, else 0
```

**BESS, per interval and per evaluation period:**

```
Avail_BESS_MW(i)      = Σ_bus Avail_Bus_MW(bus, i) + Excused_MW(i)
Avail_Installed_%(i)  = Avail_BESS_MW(i) / Installed_Capacity_MW            -- must never exceed 100%
Avail_Contracted_%(i) = min( Avail_BESS_MW(i) / Contracted_Capacity_MW, 200% )
Avail_BESS_MW(ep)     = Σ_i Avail_BESS_MW(i) / N
```

`Excused_MW` carries excused events quantified in MW per interval. `i` is the calculation interval, `ep` the evaluation period, `N` its interval count.

## Easy to get wrong

Defaulting a data gap to last-known instead of 0. Summing strings and modules on a bus instead of taking the `min` of the two sides. Clipping the contracted view at 100%, which hides exactly the margin-consumption signal the two-denominator design exists to show. Using voltage as an input rather than as a lie detector on the status signals.

## References

[M01 E02 Equipment Availability Deep Dive](../../BESS_Experts/m01-e02-equipment-availability-deep-dive.pdf); implemented in [Daily Performance Report §2.1](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md).
