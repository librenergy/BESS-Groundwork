---
type: Template
title: "MT_POM_001 — Operating RTE"
description: "Owner metric: monthly metered round-trip efficiency at the commercial boundary, SOC-matched; the trend the RTE test will confirm. Leads PG_103 and PG_203."
---

# `MT_POM_001` — Operating RTE

**Class:** monitoring · **Segment grain:** `POM` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** The efficiency the plant **actually ran at** over the month, from the revenue meter, between matched states of charge. It is **not** the tested RTE (`PG_103` / `PG_203` are controlled tests on a protocol, and read higher), not a per-cycle figure, and not a settlement quantity. It is the leading indicator: an operating RTE drifting down for three months predicts the test before the test is scheduled.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_POM_001` |
| **Formula** | over a window `[t0, t1]` chosen so that `|soc_kwh(t1) − soc_kwh(t0)| ≤ {{tol}}` MWh (installed basis): `RTE = Σ energy_exported / Σ energy_imported` at the POM. Monthly value: the SOC-matched window closest to the calendar month; rolling 30-day as the trend. Auxiliary treatment stated: `{{gross at the POM, auxiliaries inside the boundary}}` or `{{auxiliaries metered separately and reported beside}}`. **Gaps:** a meter gap inside the window voids the window (`null`, flagged), never filled from PCS data; a month with no SOC-matched window of at least `{{d}}` days is `null`. |
| **Units** | %; secondary: the residual SOC mismatch in MWh |
| **Measurement boundary** | the POM revenue meter, the same boundary as the settlement and as the RTE guarantees; state the voltage level and whether auxiliaries sit inside it |
| **Inputs & sources of truth** | `energy_exported` / `energy_imported` from the **revenue meter** (wins over PCS sums and EMS energy counters, always: meter for money); `soc_kwh_installed` at BESS grain (**master BMS**) for window matching; the auxiliary meter where one exists (`MT_POM_002`) |
| **Secondary / cross-check source** | PCS-summed energy for the energy-balance check (meter vs PCS within `{{x}}`%); the last tested RTE as the ceiling |
| **Calculation interval** | monthly, plus a rolling 30-day series updated daily |
| **Aggregation** | none beyond the window; do not average monthly RTEs into a yearly one (sum the energies instead) |
| **Timezone & clock convention** | `{{TZ}}`, calendar; the RTE guarantees run on their own test dates and contract clocks, kept on their sheets |
| **Exclusions** | test windows (`PG_103` / `PG_203` tests) excluded from the operating figure and reported separately |
| **Target / alert** | no contractual target. Alert when the rolling figure drops more than `{{x}}` pp against the trailing 3-month mean, and whenever it exceeds the last tested RTE (an energy-balance error, not a result). Review monthly |
| **Related PG** | leads `PG_103` and `PG_203` |
| **Owner of the calculation** | `[PE]`, materialised monthly with the window bounds stored |
| **Reporting chain** | Monthly Performance Report, dashboards |
| **Reconciliation / plausibility checks** | `RTE < 100%` always and `≤` tested RTE; SOC residual within tolerance and published; meter vs PCS energy balance; temperature context beside the number (a cold month reads low) |
| **Known discrepancies** | Operating RTE reads **below** tested RTE by `{{typically 2 to 6}}` pp: partial cycles, auxiliaries, temperature, standby losses. Expected, and nobody "fixes" it. Settlement RTE, where a market defines one, is a different quantity on a different window |

## Easy to get wrong

Computing over a calendar month without SOC matching, which turns a stored-energy change into an efficiency change. Reading energies from the PCS or EMS instead of the meter. Averaging monthly RTEs. Letting a test window inflate the operating figure.
