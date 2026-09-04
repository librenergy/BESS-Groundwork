---
type: Template
title: "PG_102 — Offtake Capacity (Power Test)"
description: "Calculation sheet for the offtake capacity test: the metered power test at the POI that re-prices contract capacity, its preconditions, re-test rights and deemed rules, the energy dimension it usually does not guarantee, and illustrative SQL. Evidenced by MT_BESS_004 and MT_BESS_005."
---

# PG_102 — Offtake Capacity (Power Test)

**Source:** `{{OFFTAKE_AGREEMENT}}` §`{{CLAUSE}}` and test protocol exhibit `{{EXHIBIT}}` ([extraction](../../Project_Documentation/PPA/guarantees.md)). **Direction:** owed to the offtaker; the owner conducts and reports the test. **Guaranteed level:** `{{Contract_MW}}` ❓ per agreement, `{{duration}}` h nameplate. **Owner metrics:** evidenced by [`MT_BESS_004`](../../Metrics_Tree%28MT%29/calculations/MT_BESS_004-power-availability.md) (PA) and, where energy is measured, [`MT_BESS_005`](../../Metrics_Tree%28MT%29/calculations/MT_BESS_005-energy-capacity.md) (QA).

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields. The [reference position](../performance-guarantee-matrix.md#12-the-reference-position) is pre-filled as the method wherever the agreement is silent. Matrix row: [PGM §2](../performance-guarantee-matrix.md#2-owed-to-the-offtaker).

## Evaluation period

| | |
|---|---|
| Cadence | per test; `{{one scheduled test per contract year}}` plus `{{n}}` on-demand tests at the offtaker's request ❓ |
| Clock | the result applies from `{{the test-report date / the first day of the next month}}` until the next test |
| Computed by | the owner runs the test; the offtaker (or its IE) witnesses; ❓ who signs the result |
| Window | `{{season / ambient window}}`; weather or consent constraints ❓ §`{{CLAUSE}}` |

## Formula

```
Tested_Capacity_MW  = mean( P_POI(i) )  over the full-rate discharge plateau
Capacity_Ratio      = min( Tested_Capacity_MW / Contract_MW, 1 )
Adjusted_Contract_MW = Contract_MW × Capacity_Ratio          -- carried into PG_101's denominator and the capacity payment
```

**Reference position (where the protocol is silent):** plateau defined from ramp completion to first derate, at the POI revenue meter. **Energy delivered over the full discharge is measured and reported beside the power figure**, even where no instrument prices it.

| Term | Definition | Source |
|---|---|---|
| `P_POI(i)` | Net active power at the measurement boundary, `{{1}}` min | revenue meter, §`{{CLAUSE}}` |
| Plateau | `{{from t_ramp_complete to t_first_derate / fixed n minutes}}` | protocol ❓ |
| `Contract_MW` | Contract capacity as last adjusted | §`{{CLAUSE}}`; ❓ if anchored to the supply agreement's guaranteed power |
| Preconditions | SOC at `{{100% contracted}}`, ambient within `{{range}}`, ramp rates configured `{{x}}`%/min | protocol |
| `E_test` | MWh delivered from full to the protocol's end-of-discharge condition | protocol; **reported, ❓ guaranteed?** |

## Conditions and exclusions (complete list)

1. **Measurement boundary.** `{{revenue meter at the POI, HV side}}` ❓, net or gross of auxiliary load (state it). The vendor tests `PG_204` / `PG_202` at `{{its own boundary}}`; the difference between the two is transformer and auxiliary losses, and the two results are not derivable from one another.
2. **Exclusions and excuse events.** A test aborted by a grid event, offtaker instruction, or force majeure is `{{void and rescheduled / counted}}` ❓. Confirm whether an offtaker-requested test that the plant passes is charged to the offtaker.
3. **Notice conditions.** Test notice `{{n}}` days ahead; offtaker witnessing rights; the report due `{{n}}` days after the test.
4. **Caps on exclusions.** Number of re-tests permitted after a failure `{{n}}`, within `{{days}}`; whether the LD runs during the re-test window.
5. **Data-gap rules.** A missing or invalid meter interval inside the plateau `{{voids the test / is interpolated}}` ❓. Under the reference: void and repeat.
6. **Adjustment tables.** Usually **none** on the offtake side: the test counts as measured. The vendor's tests may carry a temperature correction, so one physical campaign can pass vendor-side and fail here. Record both.
7. **Deemed values.** Failure to test by `{{date}}` → capacity deemed `{{last tested / zero}}` ❓; the deemed-zero variant is a cliff worth flagging.
8. **Termination or freeze triggers.** `Capacity_Ratio` below `{{floor}}` `{{once / for n consecutive tests}}` → `{{event of default / termination right}}` ❓ §`{{CLAUSE}}`.

## Edge cases and traps

- **A single test prices a year.** One bad afternoon sets the capacity payment until the next test. Schedule for best conditions, and hold the on-demand re-test right in view.
- **Power averaged over the plateau does not measure duration.** Degradation shortens the plateau without lowering its average, so a degraded plant passes a power test. Verify line by line whether any level, adjustment, LD or default references energy; if none does, record the gap and the offtaker's likely fallback (partial unavailability under `PG_101`).
- **The taper is imbalance, not degradation.** The end-of-discharge power drop reflects string SOC divergence; keep it out of the plateau and book it to `MT_BESS_007`.
- **LD from the report date.** Where LD accrues per day from the test-report date, the reporting deadline is money.
- **One campaign, several contracts.** Align the offtake test with `PG_202` / `PG_204` where the bases match, and record where they do not (temperature adjustment, boundary, SOC window, seasonal consent).
- **`Contract_MW` anchors elsewhere.** If contract capacity is defined by reference to the supply agreement's guaranteed power, a document not held sets this denominator.

## Data and metrics required to deliver it

1. **Authoritative input.** The signed test record (`perf_test`: meter reads bracketing the discharge, plateau bounds, ambient, SOC at start, ramp settings, result) and `{{1}}` min revenue-meter data for the full test window.
2. **Supporting evidence.** SOC trace from the master BMS (installed and contracted basis), ambient and cell temperatures, PCS derate flags, the notice and witnessing record, and any grid or offtaker event overlapping the test.
3. **Derived series.** `Capacity_Ratio` history against the guarantee; `E_test` beside it (the energy figure the offtaker sees on every test); taper area; the operating cross-check from `MT_BESS_004` on the test day (declared capability must not exceed tested capability).

**Capture before the first test:** the meter data retention and the SOC trace; the test is unrepeatable.

## Measurement convention (proposed, pending counterparty agreement)

Where the protocol names the plateau but not its bounds:

```
t_start   = first interval with P_POI ≥ 0.98 × setpoint after the ramp
t_end     = last interval before P_POI falls below 0.98 × setpoint for ≥ {{2}} consecutive intervals
Plateau   = [t_start, t_end], minimum {{n}} min or the test is void
E_test    = Σ export_mwh from t_start to the protocol's end-of-discharge SOC, reported beside the power figure
```

- The 98% band is the revenue meter's class plus setpoint jitter; state the project value.
- **Vehicle:** the test procedure submitted for approval under §`{{CLAUSE}}`. **Deadline:** before the first scheduled test.

## Pseudo-SQL

> Illustrative only: placeholder names, generic dialect, not runnable; real names come from the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md). **Where this disagrees with the formula above, the formula wins.** Shared schema in the [folder index](index.md).

```sql
WITH p AS (
    SELECT MAX(CASE WHEN param_key = 'contract_capacity_mw' THEN param_value_num END) AS contract_mw
    FROM contract_param
),
t AS (   -- the signed record: T5..T8 bracket the discharge leg
    SELECT test_id, test_ts, power_capacity_mw,
           meter_export_t8_mwh - meter_export_t5_mwh AS e_test_mwh,          -- reported, not guaranteed
           test_result
    FROM perf_test
    WHERE contract = 'OFFTAKE' AND test_id = :test_id
),
plateau AS (   -- owner cross-check from interval data, per the proposed convention
    SELECT AVG(export_mwh * 60.0 / interval_min) AS plateau_mw
    FROM meter_interval
    WHERE ts >= :plateau_start AND ts < :plateau_end AND quality_flag = 'GOOD'
)
SELECT t.test_ts,
       t.power_capacity_mw                                        AS tested_mw,
       LEAST(t.power_capacity_mw / NULLIF(p.contract_mw, 0), 1)  AS capacity_ratio,
       p.contract_mw * LEAST(t.power_capacity_mw / NULLIF(p.contract_mw, 0), 1) AS adjusted_contract_mw,
       t.e_test_mwh,
       pl.plateau_mw - t.power_capacity_mw                        AS record_vs_meter_delta_mw
FROM t CROSS JOIN p CROSS JOIN plateau pl;
```

**Easy to get wrong:** the record is the contractual input and the meter query is only the cross-check; a difference between them is a finding about the test, not a correction to it. `e_test_mwh` appears in the output deliberately: the energy the offtaker sees on every report is the energy they will argue about, even with no clause behind it. The deemed rule (condition 7) is not expressible here; no row exists to query when the test was not run.

## Reporting and verification

Test report within `{{n}}` days of the test in `{{form}}`; offtaker review `{{n}}` days; the adjusted capacity applies from `{{date rule}}`. Deemed acceptance after silence ❓ §`{{CLAUSE}}`.

## LD (reference)

Capacity payment × `Capacity_Ratio`, and/or `{{rate}}` per MW shortfall `{{per day from the report date / per month}}`, capped at `{{cap}}`. §`{{CLAUSE}}`.

## Open items

- ❓ Plateau definition in the protocol; propose the convention above if silent.
- ❓ Whether `E_test` is guaranteed anywhere (level, adjustment, LD, default); record the answer either way.
- ❓ Temperature or ambient window, and whether the vendor's test adjustments differ.
- ❓ Deemed-capacity rule when a test is missed.
- ❓ Which document defines `Contract_MW`.
