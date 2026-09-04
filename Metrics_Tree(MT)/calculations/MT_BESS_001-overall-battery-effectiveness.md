---
type: Template
title: "MT_BESS_001 — Overall Battery Effectiveness (OBE)"
description: "Owner metric: the composite availability of the BESS, the weakest of the four availability types in each interval averaged over the period. Evidences PG_201."
---

# `MT_BESS_001` — Overall Battery Effectiveness (OBE)

**Class:** engineering · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** The single availability figure the owner runs the plant by: in every interval, the **weakest** of Equipment, Operational, Power and Energy availability, averaged over the period. It is a composite of four owner metrics and defines nothing of its own. It is **not** the contractual availability under either agreement: `PG_101` and `PG_201` have their own formulas, clocks and excusals and are different numbers on purpose. OBE evidences `PG_201` (the vendor's power availability guarantee) and explains `PG_101`; it never shares an identifier with either and is never reconciled to them.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_001` |
| **Formula** | `OBE(ep) = (1/N) × Σ_i min( EA_%(i), OA_%(i), PA_%(i), QA )`. `EA`, `OA`, `PA` are interval series from [`MT_BESS_002`](MT_BESS_002-equipment-availability.md), [`MT_BESS_003`](MT_BESS_003-operational-availability.md), [`MT_BESS_004`](MT_BESS_004-power-availability.md); `QA` is the scalar from [`MT_BESS_005`](MT_BESS_005-energy-capacity.md). **Gaps:** a missing `EA` or `PA` term makes the interval **0** (an unproven interval is not an available interval; genuine telemetry loss is credited back through excused events inside `EA`). `OA` is undefined outside dispatch and is then **omitted from the min**, never deemed. No `QA` before the first test: `QA = 100%` with a `no_test_yet` flag on every published figure. |
| **Units** | % (two series: installed basis and contracted basis, per the denominators in `MT_BESS_002`) |
| **Measurement boundary** | POI-normalised, inherited from the constituents ([Daily Report §9.2](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md)) |
| **Inputs & sources of truth** | `EA_%(i)`, `OA_%(i)`, `PA_%(i)` and `QA` exactly as their sheets publish them; this sheet reads the materialised series and never recomputes a constituent. Where two platforms carry a constituent, the one named on that constituent's sheet wins. |
| **Secondary / cross-check source** | The vendor's own availability figure under `PG_201`, for divergence tracking only (never as an input) |
| **Calculation interval** | `{{1 to 5}}` min, the constituents' common interval; minimum valid intervals per hour `{{n}}` ([Daily Report §9.3](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md)) |
| **Aggregation** | interval `min` → hourly mean → daily → month → contract year; the mean of the interval minima, never the minimum of the period means |
| **Timezone & clock convention** | `{{TZ}}`, calendar engineering series. Per-contract clocks (contract year, operating year) belong to the PG sheets, never here |
| **Exclusions** | none at this level; excused events act inside `EA` ([Outage Tracker](../../Data_Product%28DP%29/Outage_Tracker/outage-tracker.md)) |
| **Target / alert** | no contractual target. Alert on a day-over-day drop of more than `{{x}}` pp on either basis, and whenever the installed-basis figure exceeds 100% (a denominator error, never a result). Review daily |
| **Related PG** | explains `PG_201` (through `MT_BESS_004`) and `PG_101` (through `MT_BESS_002`); the guarantee nodes resolve to the constituents, not to OBE |
| **Owner of the calculation** | `[PE]`, materialised in the platform at interval grain; dashboards and reports read the stored series |
| **Reporting chain** | Daily Performance Report §2 (raw and depth-weighted), dashboards, Monthly Performance Report |
| **Reconciliation / plausibility checks** | `OBE(ep) ≤` every constituent's period mean; depth-weighted `≥` raw; installed basis `≤ 100%`; a step change is investigated against the constituent that moved, never smoothed |
| **Known discrepancies** | Reads **below** the vendor's `PG_201` number wherever the agreement uses `max(charge, discharge)` for power availability, and below `PG_101` wherever the offtake excuses events the plant did not deliver through. Both are expected; neither is fixed here |

## Method

**The combination.** A minimum rather than a product or an average, because the four are not independent failure modes. They are four ways the same interval can fail to serve the contract, and the weakest one sets the ceiling. Averaging would let healthy equipment mask a dispatch failure.

```
OBE(ep) = (1 / N) × Σ_i min( EA_%(i), OA_%(i), PA_%(i), QA )
```

**Two denominators, two series.** Every constituent is published against installed and against contracted capacity; OBE is computed once per basis. Watch the gap: healthy against contract while falling against installed means the overbuild is being consumed, which is the earliest reliability warning.

**Raw and depth-weighted.** Publish both. Depth weighting forgives power shortfalls at the SOC rails where deliverability is physics rather than a fault (`weight(i) = (SOC(i)/threshold)^exponent` below the discharge threshold, mirrored above the charge threshold); it applies to `OA` and `PA`, never to `EA`. Parameters in [Daily Report §9.5](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md). Until they are set, headline the raw figure with SOC context beside it, not the combined number.

## Easy to get wrong

Averaging the four instead of taking the minimum. Layering `QA` per interval, which double-counts what `EA`, `OA` and `PA` already see (a string offline is `EA`; an imbalance derate is `PA`). Treating an `OA` gap outside dispatch as 0 rather than omitting it. Reconciling OBE to the vendor's figure: they are different metrics and the delta is the evidence, not the error.

## References

[M01 E01 Introduction to BESS Availability Types](../../BESS_Experts/m01-e01-introduction-to-bess-availability-types.pdf) and [M01 E06 How to Process your Availability Data](../../BESS_Experts/m01-e06-processing-availability-data.pdf); implemented in [Daily Performance Report §2](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md).
