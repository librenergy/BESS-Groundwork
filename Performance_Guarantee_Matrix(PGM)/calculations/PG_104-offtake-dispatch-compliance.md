---
type: Template
title: "PG_104 — Offtake Dispatch Compliance"
description: "Calculation sheet for the delivery-based guarantee owed to the offtaker: delivered energy against the instruction received, the control chain and where the loop closes, tolerance and directional SOC exclusion, the conversion of a setpoint stream to an hourly benchmark, and illustrative SQL. Evidenced by MT_BESS_003."
---

# PG_104 — Offtake Dispatch Compliance

**Source:** `{{OFFTAKE_AGREEMENT}}` §`{{CLAUSE}}` ([extraction](../../Project_Documentation/PPA/guarantees.md)). **Direction:** owed to the offtaker; ❓ who computes (the offtaker often does, from its own dispatch and settlement records). **Guaranteed level:** `{{tolerance and compliance %}}` ❓ per agreement. **Availability type:** OA. **Owner metric:** evidenced by [`MT_BESS_003`](../../Metrics_Tree%28MT%29/calculations/MT_BESS_003-operational-availability.md).

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields. The [reference position](../performance-guarantee-matrix.md#12-the-reference-position) is pre-filled as the method wherever the agreement is silent. Matrix row: [PGM §2](../performance-guarantee-matrix.md#2-owed-to-the-offtaker).

## Evaluation period

| | |
|---|---|
| Cadence | judged per `{{settlement interval / hour / event}}`, aggregated `{{monthly}}`, with `{{an annual compliance ratio}}` ❓ |
| Clock | settlement clock (`{{ISO}}` operating day, `{{TZ}}`), not the contract year |
| Computed by | ❓ `{{offtaker from its records / owner}}`; if the offtaker computes, the owner shadows |
| Due | `{{with the monthly invoice / n days after month end}}` |

## Formula

Transcribe the agreement's construction. Two shapes occur:

```
-- Shape A: per-interval energy tolerance
Deviation(h)   = |Delivered_MWh(h) − Instructed_MWh(h)|
Compliant(h)   = Deviation(h) ≤ max( Tol_% × Instructed_MWh(h), Tol_MWh )
Compliance_%   = Σ Compliant(h) / Σ Instructed(h)         -- instructed and not excused
Shortfall_MWh  = Σ max(0, Instructed_MWh(h) − Delivered_MWh(h))  over non-compliant hours

-- Shape B: per-event response (ramp, response time, holding)
Compliant(e)   = responded within {{t}} s and held within {{band}} for the event duration
```

**Reference position (where the agreement is silent):** `MT_BESS_003`. Shortfall `= max(0, |setpoint| − |actual|)` per interval, dead-band widened in proportion to ramp rate, **directional** SOC exclusion, revenue meter at the POI against the setpoint as received.

| Term | Definition | Source |
|---|---|---|
| `Instructed_MWh(h)` | Energy the instruction called for in hour `h`, from the noticed schedule or the setpoint stream (see convention) | §`{{CLAUSE}}` ❓ |
| `Delivered_MWh(h)` | Net export (or import) at the boundary | revenue meter, §`{{CLAUSE}}` |
| `Tol_%`, `Tol_MWh` | Tolerance band; `{{scarcity / event hours}}` may carry a tighter band | §`{{CLAUSE}}` |
| Response time | `{{n}}` s from instruction receipt to acknowledgement | §`{{CLAUSE}}` |
| Ramp rate | `{{x}}`%/min contractual cap | §`{{CLAUSE}}` |
| Excused hour | see conditions 2 and 7 | §`{{CLAUSE}}` |

## Control chain and measurement boundary

The setpoint's path and the point where the control loop closes decide how much of the tolerance band the plant can use.

```
{{Offtaker / SC}} --> {{RTU / RTAC}} --> {{PPC}} --> {{OEM site controller}} --> PCS
Loop closes on:      {{meter}} ❓            Compliance measured at: {{POI revenue meter}}
```

- If the loop closes upstream of the compliance meter (at the POM or the inverter terminals), the losses between them consume part of the band on every discharge, systematically. Quantify the loss at rated power and compare it with `Tol_%`.
- If no document shows the loop closing on the compliance meter, that is an **open ask to the controls integrator**, recorded below.
- The setpoint **as received at the PPC** is the record to keep; the offtaker's sent log is the reconciliation source, and any gap between the two is a communications finding, not a compliance one.

## Conditions and exclusions (complete list)

1. **Measurement boundary.** `{{POI revenue meter}}` at settlement resolution; import and export judged separately or as net ❓.
2. **Exclusions and excuse events.** Transcribe; expected: force majeure, grid operator instruction overriding the offtaker's, an instruction outside declared capability or outside the operating envelope, SOC at the rail in the direction of the request, an instruction received late or malformed. For each, the vendor side normally has **no counterpart** (`PG_206` is usually absent), so every non-excused deviation is retained owner risk.
3. **Notice conditions.** Instruction valid only if received `{{t}}` before the interval via `{{channel}}`; capability declarations due `{{when}}`; a deviation notice from the owner within `{{n}}` hours of an event.
4. **Caps on exclusions.** `{{n}}` excused deviations per `{{period}}`; hours of declared unavailability count against `PG_101` instead.
5. **Data-gap rules.** Missing setpoint record with meter data present is an investigation, not zero; missing meter interval → settlement estimate rules apply ❓ §`{{CLAUSE}}`.
6. **Adjustment tables.** None expected; a loss adjustment between loop meter and compliance meter is the owner's to argue (see control chain).
7. **Deemed values.** ❓ an instruction is deemed received when sent; a non-acknowledged instruction is deemed `{{compliant / non-compliant}}`.
8. **Termination or freeze triggers.** Compliance below `{{floor}}` for `{{n}}` months → `{{default}}` ❓.

## Edge cases and traps

- **Over-delivery is never a credit** but may be a deviation. A band that is symmetric penalises delivering more than instructed; state which.
- **A power setpoint stream is not an hourly energy benchmark.** Ramps, re-instructions inside the hour and rounding decide how many MWh the instruction "called for"; that conversion is the convention below and it moves the result.
- **The SOC exclusion must be directional.** An empty battery asked to charge has no excuse; a non-directional rule forgives exactly the failures that matter.
- **Coverage is low.** Compliance exists only in instructed hours; do not quote it as a calendar-time figure.
- **Scarcity or event hours** often carry a tighter band and a higher penalty; the same deviation costs more.
- **Sent vs received.** A record built from the offtaker's sent log scores comms failures as owner non-compliance; the PPC's received log is the only defensible record.
- **The retained gap to `PG_206`.** A site-controller fault scores as owner non-compliance here and is unmeasured vendor-side.

## Data and metrics required to deliver it

1. **Authoritative input.** Setpoint as received at the PPC (`setpoint_interval`, at or above the settlement resolution) with acknowledgement flag; revenue-meter interval data; the offtaker's dispatch notices (`dispatch_notice`) for reconciliation; SOC at BESS grain for the rail test; controller fault and command-timeout flags for attribution.
2. **Supporting evidence.** Declared capability (`ACP`, `ADP`) at the time of each instruction; grid operator instructions; comms-link health logs.
3. **Derived series.** `MT_BESS_003` per interval; per-hour `Compliant(h)` and `Shortfall_MWh`; the attribution split (owner controls / provider boundary / offtaker / nobody) from [Daily Performance Report §4.2](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md).

**Capture before COD:** the received-setpoint log and the fault flags. Neither exists in any settlement record.

## Measurement convention (proposed, pending counterparty agreement)

Converting the setpoint stream to the hourly benchmark, and what is excused inside it:

```
Instructed_MWh(h) = Σ_i setpoint_mw_received(i) × Δt(i)          -- every interval of the hour, as received
Ramp windows      = the {{n}} intervals after each setpoint change; shortfall inside them judged against the ramp-limited trajectory, not the target
Dead-band         = dead_band_stable + f(ramp_rate), per MT_BESS_003, converted to MWh over the hour
SOC exclusion     = SOC = 100% with a charge setpoint, or SOC = 0% with a discharge setpoint; nothing else
Invalid instruction = malformed, late, or outside declared capability: excluded from Σ Instructed(h) and logged
Gaps              = missing meter → hour void; missing setpoint with meter present → investigation, not zero
```

- Built on the offtaker's own instruction and the shared revenue meter, so both inputs are numbers the counterparty already holds.
- **Vehicle:** the operating procedures / dispatch protocol under §`{{CLAUSE}}`. **Deadline:** pre-COD.

## Pseudo-SQL

> Illustrative only: placeholder names, generic dialect, not runnable; real names come from the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md). **Where this disagrees with the formula above, the formula wins.** Shared schema in the [folder index](index.md).

```sql
WITH p AS (
    SELECT MAX(CASE WHEN param_key = 'dispatch_tol_pct' THEN param_value_num END) AS tol_pct,   -- ❓
           MAX(CASE WHEN param_key = 'dispatch_tol_mwh' THEN param_value_num END) AS tol_mwh    -- ❓
    FROM contract_param
),
instructed AS (   -- hourly benchmark from the received stream, per the convention
    SELECT date_trunc('hour', ts) AS hour_ts,
           SUM(setpoint_mw * :interval_min / 60.0) AS instructed_mwh,
           BOOL_OR(controller_fault_flag)          AS controller_fault
    FROM setpoint_interval
    WHERE ts >= :period_start AND ts < :period_end
    GROUP BY 1
),
delivered AS (
    SELECT date_trunc('hour', ts) AS hour_ts,
           SUM(export_mwh) - SUM(import_mwh)       AS delivered_mwh,
           BOOL_OR(quality_flag <> 'GOOD')         AS meter_gap
    FROM meter_interval
    WHERE ts >= :period_start AND ts < :period_end
    GROUP BY 1
),
rail AS (   -- directional SOC exclusion at hour grain (any interval at the rail in the request direction)
    SELECT date_trunc('hour', s.ts) AS hour_ts,
           BOOL_OR( (s.soc_pct_installed >= 100 AND sp.setpoint_mw < 0)
                 OR (s.soc_pct_installed <= 0   AND sp.setpoint_mw > 0) ) AS soc_excused
    FROM soc_interval s JOIN setpoint_interval sp USING (ts)
    GROUP BY 1
),
hours AS (
    SELECT i.hour_ts, i.instructed_mwh, d.delivered_mwh, d.meter_gap,
           r.soc_excused,
           EXISTS (SELECT 1 FROM excuse_event x
                   WHERE x.contract = 'OFFTAKE' AND i.hour_ts >= x.start_ts AND i.hour_ts < x.end_ts) AS excused,
           COALESCE(n.is_scarcity_event_hour, FALSE) AS scarcity
    FROM instructed i
    JOIN delivered d USING (hour_ts)
    LEFT JOIN rail r USING (hour_ts)
    LEFT JOIN dispatch_notice n USING (hour_ts)
    WHERE i.instructed_mwh <> 0
)
SELECT COUNT(*) FILTER (WHERE NOT excused AND NOT soc_excused AND NOT meter_gap)                        AS judged_hours,
       COUNT(*) FILTER (WHERE NOT excused AND NOT soc_excused AND NOT meter_gap
                          AND ABS(delivered_mwh - instructed_mwh)
                              <= GREATEST(p.tol_pct / 100.0 * ABS(instructed_mwh), p.tol_mwh))              AS compliant_hours,
       SUM(GREATEST(0, ABS(instructed_mwh) - ABS(delivered_mwh)))
           FILTER (WHERE NOT excused AND NOT soc_excused AND NOT meter_gap)                             AS shortfall_mwh,
       SUM(CASE WHEN scarcity THEN GREATEST(0, ABS(instructed_mwh) - ABS(delivered_mwh)) ELSE 0 END)   AS scarcity_shortfall_mwh
FROM hours CROSS JOIN p GROUP BY p.tol_pct, p.tol_mwh;
```

**Easy to get wrong:** `instructed_mwh` comes from the **received** setpoint stream, not from `dispatch_notice`. Joining the notice table for the benchmark scores every comms loss as a delivery failure. The ramp-window treatment in the convention is not in this query; it is the first refinement to add, and the one that most changes the count in fast-cycling markets.

## Reporting and verification

`{{Offtaker computes with the monthly invoice / owner reports}}`; owner dispute window `{{n}}` days per §`{{CLAUSE}}`, tied to the settlement dispute clock in the [Settlement Reconciliation](../../Data_Product%28DP%29/Settlement_Reconciliation/settlement-reconciliation.md). Silence ratifies the offtaker's count.

## LD (reference)

`{{rate}}` × `Shortfall_MWh` `{{× price index during scarcity hours}}`, capped at `{{cap}}`; `{{per-event penalty}}` for response failures. §`{{CLAUSE}}`.

## Open items

- ❓ Who computes the contractual number and from which record.
- ❓ Where the control loop closes; ask the controls integrator for the document, or record the loss between loop meter and POI.
- ❓ Whether the SOC exclusion in the agreement is directional; if not, negotiate it in the operating procedures.
- ❓ Tolerance band, symmetric or not; scarcity-hour variant.
- Agree the setpoint-to-hourly-energy convention pre-COD.
