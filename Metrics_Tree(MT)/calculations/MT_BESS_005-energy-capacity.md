---
type: Template
title: "MT_BESS_005 — Energy Capacity (QA)"
description: "Owner metric: does the plant still hold the energy promised? Capacity retention from the most recent controlled test, a scalar that holds between tests. Evidences PG_202 and PG_102."
---

# `MT_BESS_005` — Energy Capacity (QA)

**Class:** engineering · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** **Long-term capacity retention**: what the plant delivered in its most recent controlled capacity test against what is guaranteed for the current year. A slow-moving signal that changes over months and years, measured with high accuracy and low frequency, and carried into OBE as a **scalar**. It is **not** state of charge, not a per-interval energy term (every fast energy effect is already captured by `EA`, `OA` or `PA`), and not the test result itself. `PG_202` and `PG_102` own the test protocol, preconditions and remedies. It evidences both and reads their test.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_005` |
| **Formula** | `QA = min( Q_test / Q_guaranteed × 100, 100 ) %`. `Q_test` is the energy measured in the most recent controlled capacity test (MWh at the POM, revenue meter); `Q_guaranteed` the contracted or guaranteed energy for the current year on the governing contract's schedule. **Holds its value** until a new test or a guaranteed-value change. **Gaps:** no test on file → `100%` with a `no_test_yet` flag on every published figure, never `null` (OBE must still compute); a failed test is a value, not a gap, and stays until the re-test. |
| **Units** | % (secondary: `Q_test` in MWh, on installed and contracted bases) |
| **Measurement boundary** | the POM revenue meter, per the test protocol; the same boundary as the guarantee it evidences, stated |
| **Inputs & sources of truth** | `Q_test` from the **test record** (the PGM's `perf_test`, revenue-meter reads; the controlled test wins over any telemetry-derived capacity estimate, always); `Q_guaranteed` from the **guarantee schedule** on the governing contract (`PG_202` for the vendor view, `PG_102` for the offtake view; ❓ if the two protocols or schedules differ, this metric follows `{{which}}` and the other is reported beside it); `contracted_energy` and installed energy (dimension data) |
| **Secondary / cross-check source** | telemetry-derived capacity estimate from full-range cycles (`MT_BESS_006`'s monthly estimate) to detect drift between tests; vendor-reported `soh_percent` for divergence only |
| **Calculation interval** | per test (`{{once or twice a year}}`), plus on every change of `Q_guaranteed` |
| **Aggregation** | none; a scalar with a validity window from its test date to the next. The **test history** (date, result, pass/fail, guaranteed level, re-tests) is the series |
| **Timezone & clock convention** | the governing contract's year for `Q_guaranteed` (contract year vs operating year, stated); test date in `{{TZ}}` |
| **Exclusions** | none. Test preconditions and remedies are the PG sheet's |
| **Target / alert** | no contractual target here (the guaranteed level is `PG_202` / `PG_102`'s). Alert when a test result lands within `{{x}}` pp of the guaranteed level, and when the telemetry cross-check drifts more than `{{y}}` pp from the last test. Review per test and monthly |
| **Related PG** | evidences `PG_202` and `PG_102` |
| **Owner of the calculation** | `[PE]`, stored as a versioned scalar with its test reference; never recomputed at read |
| **Reporting chain** | Daily Performance Report §2.4 and §9.1, `MT_BESS_001`, Monthly Performance Report (test history against the guarantee schedule) |
| **Reconciliation / plausibility checks** | read the trace, not just the headline: the **discharge taper** at the end of the test is imbalance, not degradation (book it separately, it is recoverable); charge energy that is not usable at rated power is excluded from `Q_test`; test conditions (temperature, SOC start, ramp) recorded and compared to the protocol; a re-test pass after a failure is a different contractual position from never failing, and the history shows it |
| **Known discrepancies** | A capacity test runs in best-case conditions: `Q_test` is an **upper bound** on a bad day. `QA` therefore reads above what operating data would suggest; expected. SOC in percent hides the installed/contracted basis, so `Q_test` is stated in MWh |

## Method

```
QA = min( Q_test / Q_guaranteed × 100, 100 ) %
```

**Why a scalar.** Every fast energy effect is already captured: a string offline is `EA`, a power derate from imbalance is `PA`, a shortfall from reduced capability is `OA`. A single offline string does not reduce site energy capacity because rebalancing recovers it within minutes. Layering an interval energy term on top of the other three double-counts what they already see. The one thing nothing else captures is the slow, permanent fade no rebalance can fix, and only a controlled test measures it.

**Two features to capture from every test trace.** First, the end-of-discharge power taper, where SOC divergence between strings forces a derate before nominal energy is delivered; the area lost is imbalance, so feed it to `MT_BESS_007`. Second, charge energy that went in but will not come back out at rated power.

## Easy to get wrong

Making it an interval series. Booking the taper as degradation and writing off recoverable capacity. Taking the vendor's SoH as `Q_test`. Losing the test history so a re-test pass looks like a clean record. Comparing SOC percentages across systems without stating the basis.

## References

[M01 E05 Energy Availability Deep Dive](../../BESS_Experts/m01-e05-energy-availability-deep-dive.pdf) (the episode keeps the old name; the metric is Energy Capacity); implemented in [Daily Performance Report §2.4](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md).
