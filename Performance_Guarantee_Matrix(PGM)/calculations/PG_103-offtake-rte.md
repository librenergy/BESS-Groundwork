---
type: Template
title: "PG_103 — Offtake Efficiency (RTE)"
description: "Calculation sheet for the round-trip efficiency guarantee owed to the offtaker: the metered test cycle at the POI, the boundary and auxiliary-load question that decides the number, SOC matching, and illustrative SQL. Led by MT_POM_001."
---

# PG_103 — Offtake Efficiency (RTE)

**Source:** `{{OFFTAKE_AGREEMENT}}` §`{{CLAUSE}}` and test protocol `{{EXHIBIT}}` ([extraction](../../Project_Documentation/PPA/guarantees.md)). **Direction:** owed to the offtaker; the owner conducts and reports. **Guaranteed level:** `{{RTE_%}}` ❓ per agreement, `{{flat / by contract year}}`. **Owner metric:** led by [`MT_POM_001`](../../Metrics_Tree%28MT%29/calculations/MT_POM_001-operating-rte.md) (operating RTE, the trend that predicts the test).

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields. The [reference position](../performance-guarantee-matrix.md#12-the-reference-position) is pre-filled as the method wherever the agreement is silent. Matrix row: [PGM §2](../performance-guarantee-matrix.md#2-owed-to-the-offtaker).

## Evaluation period

| | |
|---|---|
| Cadence | per test, `{{annually, usually in the same campaign as PG_102}}`; ❓ some agreements also measure an operating RTE over the year |
| Clock | result applies from `{{report date}}` to the next test; operating variant on the contract year |
| Computed by | the owner; offtaker or IE witnesses |
| Window | `{{ambient window}}`; a cold test reads low |

## Formula

```
RTE_test = E_discharged / E_charged           over one full test cycle, SOC-matched start and end

-- Operating variant, where the agreement measures it (reference: MT_POM_001):
RTE_op   = Σ export_mwh / Σ import_mwh        over a window with |SOC(t1) − SOC(t0)| ≤ {{tol}} MWh
```

**Reference position (where the protocol is silent):** one declared boundary used for charge, discharge and throughput alike; SOC-matched window; the raw figure reported beside the matched one; suppressed below one full cycle rather than printed.

| Term | Definition | Source |
|---|---|---|
| `E_charged` | Import MWh at the boundary from the start of charge to the end of charge, including any rest period the protocol counts | §`{{CLAUSE}}` |
| `E_discharged` | Export MWh at the boundary from the start of discharge to the protocol's end-of-discharge condition | §`{{CLAUSE}}` |
| Boundary | `{{POI revenue meter}}` ❓; auxiliary load `{{inside / outside}}` | §`{{CLAUSE}}` |
| SOC window | `{{SOC_min to SOC_max}}` on `{{installed / contracted}}` basis | protocol ❓ |
| Rest period | `{{n}}` min between charge and discharge, auxiliaries `{{counted / not}}` | protocol |
| Temperature adjustment | usually **none** on the offtake side | compare `PG_203` |

## Conditions and exclusions (complete list)

1. **Measurement boundary.** The lever that moves the number most: RTE is discharge ÷ charge, so every loss between the batteries and the meter counts twice (once each way). At the POI with auxiliaries inside, expect `{{2 to 6}}` pp below the vendor's `PG_203` figure at its own boundary. State the voltage level, the meter, and the auxiliary treatment.
2. **Exclusions and excuse events.** Test aborted by grid event, offtaker instruction or force majeure → `{{void / rescheduled}}` ❓. Auxiliary consumption during an offtaker-imposed hold `{{counted / excluded}}`.
3. **Notice conditions.** Test notice `{{n}}` days; witnessing; report due `{{n}}` days after.
4. **Caps on exclusions.** Re-tests permitted `{{n}}` within `{{days}}`.
5. **Data-gap rules.** A meter gap inside the cycle voids the test; never fill from PCS or EMS counters.
6. **Adjustment tables.** ❓ usually none; if the protocol carries a temperature correction, transcribe it and compare with the vendor's.
7. **Deemed values.** Missed test → RTE deemed `{{last tested / guaranteed}}` ❓.
8. **Termination or freeze triggers.** RTE below `{{floor}}` `{{for n tests}}` → `{{default}}` ❓.

## Edge cases and traps

- **Auxiliaries inside the boundary.** HVAC during a summer test can cost several points on its own. If the protocol counts the rest period, a long hold in hot weather is a failed test.
- **Partial cycles.** A protocol that does not return the plant to the starting SOC turns stored energy into apparent efficiency (or inefficiency). SOC-match, and publish the residual in MWh.
- **Boundary mismatch with `PG_203`.** The vendor can pass at the PCS terminals while the owner fails at the POI, with nothing to claim. The gap between the two boundaries is the owner's transformer and auxiliary design, priced by this guarantee.
- **RTE and capacity from one cycle.** Where `PG_102` and this test share a discharge, the plateau definition and the end-of-discharge condition must be the same in both sheets.
- **Standby losses between legs.** Auxiliaries and self-discharge during a rest period accrue to `E_charged` under most protocols.
- **Operating RTE as evidence.** An operating trend drifting down for three months predicts the test; use `MT_POM_001` to schedule maintenance or a rebalance before the test, not after.

## Data and metrics required to deliver it

1. **Authoritative input.** The signed test record (`perf_test`: import and export meter reads bracketing each leg, timestamps, SOC at each leg boundary, ambient) and `{{1}}` min revenue-meter data for the whole cycle.
2. **Supporting evidence.** SOC trace (master BMS, basis stated), auxiliary meter where one exists (`MT_POM_002`), ambient and cell temperature, PCS operating modes during the rest period, any overlapping event.
3. **Derived series.** `RTE_test` history against the guaranteed level; `MT_POM_001` monthly beside it; the energy balance check (meter vs PCS-summed energy within `{{x}}`%).

**Capture before the first test:** auxiliary sub-metering if the boundary includes it; without it the loss cannot be attributed, only observed.

## Measurement convention (proposed, pending counterparty agreement)

```
Cycle     = charge from SOC_start to SOC_max, rest {{n}} min, discharge to SOC_start (installed basis, ± {{tol}} MWh)
E_charged = Σ import_mwh over [charge start, discharge start)      -- rest period inside the charge leg
E_disch   = Σ export_mwh over [discharge start, discharge end]
RTE_test  = E_disch / E_charged ; residual SOC mismatch reported in MWh beside it
Aux       = auxiliary meter MWh over the cycle, reported separately whatever the boundary
```

- One boundary for both legs, declared once; the alternative (charge at one meter, discharge at another) is not an efficiency.
- **Vehicle:** the test procedure under §`{{CLAUSE}}`. **Deadline:** before the first test.

## Pseudo-SQL

> Illustrative only: placeholder names, generic dialect, not runnable; real names come from the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md). **Where this disagrees with the formula above, the formula wins.** Shared schema in the [folder index](index.md).

```sql
WITH t AS (   -- T1..T4 bracket the charge leg, T5..T8 the discharge leg
    SELECT test_id, test_ts,
           meter_import_t4_mwh - meter_import_t1_mwh AS e_charged_mwh,
           meter_export_t8_mwh - meter_export_t5_mwh AS e_discharged_mwh
    FROM perf_test
    WHERE contract = 'OFFTAKE' AND test_id = :test_id
),
g AS (
    SELECT guaranteed_pct
    FROM guarantee_schedule
    WHERE contract = 'OFFTAKE' AND metric = 'EFFICIENCY' AND year_n = :contract_year
),
soc AS (   -- SOC-match check from the owner's own trace
    SELECT MAX(CASE WHEN ts = :cycle_start THEN soc_mwh END) AS soc_start_mwh,
           MAX(CASE WHEN ts = :cycle_end   THEN soc_mwh END) AS soc_end_mwh
    FROM soc_interval
)
SELECT t.test_ts,
       t.e_discharged_mwh / NULLIF(t.e_charged_mwh, 0)              AS rte_test,
       g.guaranteed_pct / 100.0                                      AS rte_guaranteed,
       t.e_discharged_mwh / NULLIF(t.e_charged_mwh, 0) - g.guaranteed_pct / 100.0 AS shortfall,
       soc.soc_end_mwh - soc.soc_start_mwh                          AS soc_residual_mwh   -- must be within :tol
FROM t CROSS JOIN g CROSS JOIN soc;
```

**Easy to get wrong:** which meter reads bracket the rest period. Putting the rest inside the discharge leg instead of the charge leg changes nothing in the arithmetic but everything in the argument about who owns the auxiliary load during the hold. The SOC residual is a validity test, not a correction: a test outside tolerance is void, not adjusted.

## Reporting and verification

Report within `{{n}}` days in `{{form}}`; review `{{n}}` days; deemed acceptance ❓ §`{{CLAUSE}}`.

## LD (reference)

`{{rate}}` per pp below `{{RTE_%}}`, or a price adjustment `{{formula}}`, capped at `{{cap}}`. §`{{CLAUSE}}`.

## Open items

- ❓ Boundary and auxiliary treatment, stated in the agreement or agreed in the protocol.
- ❓ Rest-period rule and SOC window.
- ❓ Whether an operating RTE is measured in addition to the test.
- ❓ Compare the test cycle with `PG_203`'s protocol; record the boundary gap in pp once both are known.
