---
type: Template
title: "PG_101 — Offtake Availability"
description: "Calculation sheet for the availability guarantee the owner gives the offtaker: formula shape, the full conditions and exclusions checklist, traps, the data needed to deliver the number, a proposed derate-measurement convention, and illustrative SQL. Evidenced by MT_BESS_004 and MT_BESS_002."
---

# PG_101 — Offtake Availability

**Source:** `{{OFFTAKE_AGREEMENT}}` §`{{CLAUSE}}` ([extraction](../../Project_Documentation/PPA/guarantees.md)). **Direction:** owed to the offtaker; the owner is the reporting party. **Guaranteed level:** `{{LEVEL}}` ❓ per agreement. **Availability type:** ❓ resolve per [PGM §1.1](../performance-guarantee-matrix.md#11-availability-is-four-different-measures-and-a-contract-usually-guarantees-one); the reference is **PA**. **Owner metrics:** evidenced by [`MT_BESS_004`](../../Metrics_Tree%28MT%29/calculations/MT_BESS_004-power-availability.md) (PA) and [`MT_BESS_002`](../../Metrics_Tree%28MT%29/calculations/MT_BESS_002-equipment-availability.md) (EA).

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields. The [reference position](../performance-guarantee-matrix.md#12-the-reference-position) is pre-filled as the method wherever the agreement is silent; where the agreement defines a method, transcribe it, and the reference becomes the shadow calculation reported beside it. Matrix row: [PGM §2](../performance-guarantee-matrix.md#2-owed-to-the-offtaker).

## Evaluation period

| | |
|---|---|
| Cadence | ❓ typically annual, sometimes monthly with an annual true-up. `{{CADENCE}}` |
| Clock | `{{contract year from COD / calendar year}}`; state which instrument defines COD if several dates circulate |
| Computed by | the owner (delivered, not shadowed); the offtaker reviews |
| Due | `{{n}}` days after period end, per §`{{CLAUSE}}` |

## Formula

Transcribe the agreement's formula verbatim. The two shapes that occur, so the transcription can be checked against one:

```
-- Shape A: event-based (reads the outage log; no meter input anywhere)
Availability = ( H − (TF + max(TP − Cap, 0)) − (min(TP, Cap) + TT) − FM )
             / ( H − (min(TP, Cap) + TT) − FM )

-- Shape B: telemetry-based (reads an interval series)
Availability = Σ_i ( min(Available_MW(i), Contract_MW) + Excused_MW(i) ) / ( Contract_MW × N )
```

**Reference position (where the agreement is silent):** Shape B with `Available_MW(i) = PA_MW(i)` from `MT_BESS_004`, excused capacity credited **in the denominator's equivalent** (added back at contract MW so excused time is neutral), at `{{1 to 5}}` min intervals.

| Term | Definition | Source |
|---|---|---|
| `H` | Hours in the period | calendar × `{{TZ}}` |
| `TF` | Forced-outage equivalent hours: `Σ duration × (Unavailable_MW / Contract_MW)` | §`{{CLAUSE}}` |
| `TP` | Planned-outage equivalent hours, noticed on time | §`{{CLAUSE}}` |
| `Cap` | Planned-outage allowance per period | §`{{CLAUSE}}` ❓ |
| `TT` | Performance-test hours | §`{{CLAUSE}}` |
| `FM` | Force-majeure hours under **this** agreement's definition | §`{{CLAUSE}}` |
| `Contract_MW` | Contract capacity, as last adjusted by `PG_102` | §`{{CLAUSE}}`; ❓ if it anchors to a supply-agreement value not held |
| `Available_MW(i)` | Capability at interval `i`, at the measurement boundary | agreement, else reference `PA_MW(i)` |
| `Excused_MW(i)` | MW excused at interval `i` under this agreement | [Outage Tracker](../../Data_Product%28DP%29/Outage_Tracker/outage-tracker.md), `contract = 'OFFTAKE'` |
| `N` | Intervals in the period | derived |

Every symbol resolves to a clause. A term defined in a document not held is ❓ with the dependency named, never substituted with nameplate.

## Conditions and exclusions (complete list)

1. **Measurement boundary.** `{{revenue meter at the POI, HV side}}` ❓. Availability MW is POI-normalised: losses are directional (they reduce discharge, increase charge), and one loss figure applied both ways is wrong in one direction. State whether auxiliary load sits inside the boundary.
2. **Exclusions and excuse events.** Transcribe the full list; the expected pattern from [PGM §5](../performance-guarantee-matrix.md#5-exclusions--excuse-asymmetry) is the checklist. For each, record whether the vendor's agreement excuses it too; the rows that differ are retained owner risk.

   | Event class | Excused here? | Excused vendor-side (`PG_201`)? |
   |---|---|---|
   | Force majeure (this agreement's definition) | `{{yes/no}}` | compare word by word |
   | Grid outage / curtailment / offtaker instruction | `{{yes/no}}` | usually yes, via a different definition |
   | Offtaker-caused (failed dispatch signal, market suspension) | `{{yes/no}}` | usually excused both sides |
   | Planned maintenance, noticed on time | `{{yes, capped}}` | yes, capped separately |
   | Performance tests | `{{yes/no}}` | confirm whose tests qualify |
   | BOP forced outage (transformer, switchgear, gen-tie) | ⚠️ usually **no** | usually excused |
   | Controls / comms / command timeout | ⚠️ usually **no** | usually excused as owner-caused |
   | One-sided capability loss | fully visible | invisible under `max()` |
   | Telemetry / data gap | ❓ often no rule | low-validity hours dropped |

3. **Notice conditions.** Planned outages are excused only if noticed `{{n}}` business days ahead per §`{{CLAUSE}}`; a late notice converts the event to forced. Record who sends the notice and how receipt is evidenced.
4. **Caps on exclusions.** Planned allowance `{{h}}` equivalent hours per `{{period}}`. Overflow past the cap **moves into the forced bucket**; it does not merely lose its exclusion.
5. **Data-gap rules.** ❓ often absent on the offtake side. Under the reference: a gap is unavailable unless an Outage Tracker event credits it back. Propose this in the measurement convention below rather than leaving the gap to be argued later.
6. **Adjustment tables.** Usually none on the offtake side. Note it explicitly: the vendor's temperature and voltage adjustments (`PG_201`) excuse derates the offtaker will still count.
7. **Deemed values.** `{{deemed available during offtaker-caused events / deemed unavailable when no report is delivered}}` ❓.
8. **Termination or freeze triggers.** Availability below `{{floor}}` for `{{n}}` consecutive periods → `{{default / step-in / termination}}` ❓ §`{{CLAUSE}}`.

## Edge cases and traps

- **Annual averaging hides a bad month.** A single quarter at 90% inside a 98% year passes, and the offtaker's revenue loss sat in that quarter. Report monthly accumulators regardless of the contractual cadence.
- **Power only.** Most offtake availability measures MW, not MWh. A plant with ten minutes of energy behind it scores fully available. Where energy carries no instrument, the offtaker's fallback is reclassifying missing MWh as partial unavailability, and that closes only if the derate convention below is agreed first.
- **The one-sided derate.** A charge-only or discharge-only loss counts fully here and is invisible under a vendor `max(charge, discharge)` rule. Drafting, not the plant, created that exposure.
- **Shape A has no meter in it.** Under an event-based formula an unlogged derate does not exist; log discipline is the real control, and the daily job is reconciliation between the log and `MT_BESS_004`.
- **Excused in the numerator.** A formula that removes excused hours from the numerator only, and not the denominator, penalises the owner for outages the contract forgave. Catch it on first read.
- **Undispatched hours count.** Availability is judged around the clock, not only during dispatch; a night-time derate is chargeable.
- **Guarantee against site-level failure.** With many small units against a site-level number, only site-level events realistically threaten the guarantee. Instrument those first.

## Data and metrics required to deliver it

1. **Authoritative input.** Shape A: the [Outage Tracker](../../Data_Product%28DP%29/Outage_Tracker/outage-tracker.md) event log with start, end, unavailable MW, classification under **this** contract, and the notice record. Shape B: `MT_BESS_004` (`PA_MW`) and `MT_BESS_002` (`EA_MW`) at `{{1 to 5}}` min from owner-held signals; the offtaker will not supply them.
2. **Evidence for every exclusion claimed.** Notice timestamps and receipts, grid operator instructions, force-majeure notices, test schedules, all filed against the event they excuse.
3. **Derived series.** Running contract-year accumulator in the formula's own units; planned-allowance burn-down against `Cap` (in hours, not percent); the **With Excused @ 100%** and **Without Excused** pair from the [Daily Performance Report §3](../../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md); projected year-end availability.

**Capture before COD:** the interval series and the event log. The deemed-acceptance clock runs in the owner's favour only if the reported number survives scrutiny, and none of this can be reconstructed.

## Measurement convention (proposed, pending counterparty agreement)

Offtake agreements state what is judged (unavailable capacity) and rarely how to quantify it inside an event. Proposed rules:

```
Unavailable_MW(i) = Contract_MW − min( PA_MW(i), Contract_MW )        -- capability, rail-aware min(ACP, ADP)
Event overlay:      an Outage Tracker event with a stated MW wins over telemetry for its duration
Materiality:        Unavailable_MW(i) < {{x}} MW is 0 (noise floor of the capability signal)
Gaps:               an interval with no valid capability sample is unavailable, unless an event credits it back
Energy state:       excluded from this derate; energy shortfall is PG_102's test, not an availability event
```

- `PA_MW` uses the vendor's own reported capability signals (`ACP`, `ADP`), so the convention is traceable to a number the other side of the stack already publishes.
- **Vehicle:** the operating-procedures draft or the agreed monthly report form. **Deadline:** pre-COD; a retroactive convention is negotiated inside a dispute.

## Pseudo-SQL

> Illustrative only: placeholder names, generic dialect, not runnable; real names come from the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md). **Where this disagrees with the formula above, the formula wins.** Shared schema in the [folder index](index.md). Shape A is the worked example in the [template](calc-sheet-template.md); this is Shape B.

```sql
WITH p AS (
    SELECT MAX(CASE WHEN param_key = 'contract_capacity_mw' THEN param_value_num END) AS contract_mw
    FROM contract_param
),
cap AS (                                  -- owner-computed PA, materialised from MT_BESS_004
    SELECT ts, value_mw AS pa_mw, valid_flag
    FROM owner_metric_interval
    WHERE metric_code = 'MT_BESS_004'
      AND ts >= :period_start AND ts < :period_end
),
exc AS (                                  -- excused MW under THIS agreement only
    SELECT c.ts, COALESCE(SUM(x.excused_mw), 0) AS excused_mw
    FROM cap c
    LEFT JOIN excuse_event x
           ON x.contract = 'OFFTAKE' AND c.ts >= x.start_ts AND c.ts < x.end_ts
    GROUP BY c.ts
),
per_interval AS (
    SELECT c.ts,
           LEAST( CASE WHEN c.valid_flag THEN c.pa_mw ELSE 0 END      -- a gap is unavailable
                  + e.excused_mw, p.contract_mw ) AS credited_mw
    FROM cap c JOIN exc e USING (ts) CROSS JOIN p
)
SELECT SUM(credited_mw) / NULLIF(COUNT(*) * p.contract_mw, 0) AS availability,
       SUM(CASE WHEN credited_mw < p.contract_mw THEN 1 ELSE 0 END) AS derated_intervals
FROM per_interval CROSS JOIN p GROUP BY p.contract_mw;
```

**Easy to get wrong:** the `contract = 'OFFTAKE'` filter on the excuse ledger. The same physical event carries a different verdict under `PG_201`, and a ledger without the discriminator silently applies the vendor's excusal to the offtaker's number. The notice test (condition 3) is not in the query at all: whether an excuse row exists is an upstream judgement made when the event is adjudicated.

## Reporting and verification

Owner reports `{{monthly / annually}}` within `{{n}}` days in `{{report form}}`; offtaker has `{{n}}` days to dispute per §`{{CLAUSE}}`, after which the number is deemed accepted. The offtaker's own view (its dispatch and settlement records) is the likely basis of any challenge, so reconcile against it before issuing.

## LD (reference)

`{{rate}}` per `{{% shortfall / equivalent hour}}` × `{{Contract_MW}}`, capped at `{{cap}}` per `{{period}}`; accrual `{{mechanics}}`. §`{{CLAUSE}}`. Commercial terms are settled; the engineering payload is the methodology above.

## Open items

- ❓ Which availability type the agreement guarantees (PGM §1.1), and whether it reads the outage log or telemetry.
- ❓ `Contract_MW` anchoring: confirm which document defines it and whether `PG_102` adjusts it.
- ❓ Data-gap rule, if any; propose the convention above if silent.
- ❓ Force-majeure definition compared word by word against `PG_201`'s.
- Agree the derate-measurement convention pre-COD (vehicle and date above).
