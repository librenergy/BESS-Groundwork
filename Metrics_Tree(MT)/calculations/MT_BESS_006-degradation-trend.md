---
type: Template
title: "MT_BESS_006 — Degradation trend"
description: "Owner metric: measured state of health against the warranty curve, projected forward. Leads PG_202 and PG_102."
---

# `MT_BESS_006` — Degradation trend

**Class:** monitoring · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** Where the plant sits **against the vendor's degradation curve**, and where it will be at the next guarantee step and at end of term. Measured from capacity tests (authoritative) and estimated monthly from telemetry between tests (drift detection), on the **installed** basis. It is **not** the vendor's reported SoH (an opaque algorithm, kept as a cross-check only), not `MT_BESS_005` (which compares to the *contracted* level for the current year), and not a guarantee. It is the leading indicator that says whether `PG_202` and `PG_102` will be met before the test says so.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_006` |
| **Formula** | `SOH_test(t) = Q_test(t) / Q_installed_nominal` at each test (installed basis, augmentations reset the basis and are logged); `SOH_est(m) = Q_est(m) / Q_installed_nominal` monthly, with `Q_est` the energy delivered over full-range discharges in the month, SOC-matched, temperature-flagged; `curve_gap(t) = SOH_measured(t) − SOH_warranty(t)` in pp, where `SOH_warranty` is the contract's curve at the same age or the same cumulative EFC (state which axis the curve uses); `projection`: fit over the trailing `{{n}}` tests, evaluated at the next guarantee step date and at end of term. **Gaps:** a month with no full-range discharge has no `SOH_est` (`null`, flagged), never interpolated. |
| **Units** | % SOH; pp gap; MWh at the projection dates |
| **Measurement boundary** | the POM revenue meter for both `Q_test` and `Q_est`, so the two are comparable; DC-side estimates are not used |
| **Inputs & sources of truth** | `Q_test` from the **test record** (wins, always); `energy_exported` / `energy_imported` from the **revenue meter** and `soc_kwh_installed` from the **master BMS** for `Q_est`; the **warranty degradation curve** from the supply / service agreement (`PG_202` schedule, `PG_205` curve; ❓ clause); `cycle_count` on the vendor's convention where the curve is per EFC; `cell_temperature` statistics for flagging |
| **Secondary / cross-check source** | vendor `soh_percent` (divergence tracking only: never the source, never reconciled) |
| **Calculation interval** | per test (authoritative points); monthly (estimate) |
| **Aggregation** | points on a timeline; the projection is recomputed on every new test |
| **Timezone & clock convention** | the curve's own axis (calendar age from COD, or cumulative EFC on the vendor's convention); state it, and never mix with the offtake's cycle clock |
| **Exclusions** | test results outside protocol conditions are plotted but excluded from the fit, with the reason |
| **Target / alert** | no contractual target. Alert when `curve_gap < −{{x}}` pp, and when the projection lands below the next guaranteed level. Review monthly and per test |
| **Related PG** | leads `PG_202` and `PG_102` |
| **Owner of the calculation** | `[PE]`; test points stored with their test reference, estimates materialised monthly |
| **Reporting chain** | Monthly Performance Report (SOH vs curve chart), dashboards; feeds the augmentation planning conversation |
| **Reconciliation / plausibility checks** | `SOH_est` within `{{y}}` pp of the last `SOH_test` (a larger gap is either a bad estimate or a real event, and both get investigated); taper-attributed loss excluded from `Q_test` per `MT_BESS_005`; SOH never increases except at an augmentation |
| **Known discrepancies** | Vendor SoH is typically **higher** and smoother than the measured value; expected, algorithm unknown. The estimate reads below the test (operating conditions), expected |

## Easy to get wrong

Comparing on the contracted basis, which hides overbuild consumption. Using the vendor's SoH as the measurement. Fitting through a taper-inflated loss. Plotting against calendar age when the warranty curve is per EFC, or vice versa.
