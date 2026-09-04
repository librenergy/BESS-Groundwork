---
type: Template
title: "MT_BESS_007 — String balance"
description: "Owner metric: SOC spread across strings and the weakest string; the leading indicator of power derates and of the capacity-test taper. Leads PG_202."
---

# `MT_BESS_007` — String balance

**Class:** monitoring · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** How far apart the strings are, and which one is holding the plant back. Imbalance is what turns into a `PA` derate at the rails and into the discharge taper on the capacity test, so it moves weeks before either. Reported at BESS grain (the spread is a property of the system, computed from string inputs). It is **not** a per-string metric, not `MT_BESS_004` (which reports the consequence), and not degradation (`MT_BESS_006`): imbalance is recoverable, fade is not.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_007` |
| **Formula** | per bus and interval, over connected strings: `spread(bus,i) = max(soc_str) − min(soc_str)` in pp; `weakest(bus,i) = argmin(soc_str)` when discharging, `argmax` when charging; site headline `spread(i) = max over buses`; `n_over(i)` = strings more than `{{x}}` pp from their bus median. Daily: 95th percentile of `spread`, hours with `spread > {{x}}` pp, the most frequent `weakest` string. **Gaps:** a string with no SOC sample is dropped from that interval's population (never assumed at the median); a bus with fewer than `{{k}}` reporting strings is `null` for the interval, flagged. |
| **Units** | pp of SOC (installed basis); counts; hours |
| **Measurement boundary** | BMS-reported SOC per string; no POI normalisation applies |
| **Inputs & sources of truth** | `soc_percent_installed` per `Bat_Str` (**string BMS**, the only source); `connected` per string (**BMS**); `current` sign at the bus for the charge/discharge direction |
| **Secondary / cross-check source** | `cell_voltage` spread per string near the rails, where SOC estimation is worst and voltage is the better indicator; the capacity-test taper (`MT_BESS_005`) as the periodic confirmation |
| **Calculation interval** | `{{1 to 5}}` min |
| **Aggregation** | string population → bus spread → site max per interval; daily percentiles and time-above-threshold; weekly weakest-string ranking |
| **Timezone & clock convention** | `{{TZ}}`, calendar |
| **Exclusions** | intervals during a declared balancing operation are flagged, not excluded (the spread during balancing is the point) |
| **Target / alert** | no contractual target. Alert when `spread > {{x}}` pp for more than `{{y}}` hours in a day, and when the same string is weakest on `{{n}}` consecutive days. Review daily |
| **Related PG** | leads `PG_202`; explains `MT_BESS_004` derates |
| **Owner of the calculation** | `[PE]`, materialised at bus and BESS grain |
| **Reporting chain** | Daily Performance Report (derate attribution), dashboards (weakest-string ranking), Monthly Performance Report |
| **Reconciliation / plausibility checks** | spread is non-negative by construction; a step change in spread coincident with a BMS restart is an estimator reset, not an event; the weakest string should match the string that limits `Bus_AXP` in `MT_BESS_004` |
| **Known discrepancies** | SOC-based spread understates imbalance near the rails; the voltage cross-check is the tell. Vendor balancing reports may count a different population (excluding isolated strings) |

## Easy to get wrong

Averaging SOC across strings, which is exactly how imbalance hides. Reporting the spread per string (it is a system property). Reading an estimator reset as a balancing event. Treating imbalance loss on the test as degradation.
