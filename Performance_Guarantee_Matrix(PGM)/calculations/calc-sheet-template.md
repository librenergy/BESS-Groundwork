---
type: analysis
title: "PG_{{ID}} — {{CONTRACT}} {{METRIC}}"
description: Complete calculation methodology for a single performance guarantee - formula, inputs, conditions and exclusions, edge cases, the data required to deliver or shadow the calculation, and illustrative SQL.
---

# PG_{{ID}} — {{CONTRACT}} {{METRIC}}

**Source:** `{{CONTRACT}}` §`{{CLAUSE}}` ([extraction](../../Project_Documentation/{{DOMAIN}}/guarantees.md)). **Direction:** held by owner / owed to counterparty. **Guaranteed level:** `{{LEVEL}}`.

One sheet per guarantee mechanism, standalone so it can be reviewed on its own. Copy this file, rename it `PG_{ID}-{contract}-{metric}.md` (the guarantee’s PG code first, so sheets sort by series), carry the same code in the frontmatter title and H1 (every appearance of a metric carries its reference), and fill every section; an empty section is a finding, not an omission.

## Evaluation period

Cadence (annual / monthly / per test / hourly), the clock it runs on (contract year vs calendar vs commissioning anniversary), who computes it, and by when.

## Formula

```
{{THE FORMULA, EXACTLY AS THE CONTRACT STATES IT}}
```

| Term | Definition | Source |
|---|---|---|
| `{{TERM}}` | Resolved to its defining clause, never to an assumed value | `{{CLAUSE}}` |

Every symbol resolves to a source. A term defined in a document not held is recorded as ❓ **with the dependency named**, never substituted with nameplate.

## Conditions and exclusions (complete list)

Numbered, exhaustive. This is the section that decides disputes:

1. **Measurement boundary**: which meter, at what voltage, and what sits inside it (losses, auxiliary load). For **delivery-based guarantees** (dispatch/setpoint compliance), add a *Control chain and measurement boundary* section: how the offtaker's setpoint reaches the plant (dispatch signal → RTUs/RTACs → plant controller → OEM site controller) and at which meter the control loop closes vs where compliance is measured; a loop that settles upstream of the compliance meter consumes part of the compliance band through losses, systematically. If no document shows the loop closing on the compliance meter, that is an open ask to the controls integrator.
2. **Exclusions and excuse events**: the full list, and for each: does the *other* side's contract excuse it too? The asymmetries are the owner's exposure.
3. **Notice conditions**: any exclusion that exists only if a notice went out on time.
4. **Caps on exclusions**: planned-outage allowances and what happens to the overflow (it usually becomes chargeable, not merely un-excluded).
5. **Data-gap rules**: what happens to intervals with missing or invalid data, and whether that helps or hurts.
6. **Adjustment tables**: temperature, voltage, degradation; note where the counterpart contract has none.
7. **Deemed values**: conditions under which a number is deemed rather than measured.
8. **Termination or freeze triggers**: throughput ceilings, term expiry.

## Edge cases and traps

The failure modes a reader would not derive from the formula: annual averaging that hides a bad month, one-sided derates that score as available, a single test pricing a year, tier cliffs, interactions with a guarantee on the other side of the stack.

## Data and metrics required to deliver / shadow it

> Title the section by direction: **"…required to deliver it"** when this party is the contractual reporting party (typically guarantees *owed to the offtaker*; the deemed-acceptance clock runs in your favor only if the number survives scrutiny), **"…required to shadow it"** when the counterparty reports and you verify (typically guarantees *held from vendors*).

1. The authoritative input (event log, test record, or telemetry series) with the resolution needed.
2. Supporting evidence for every exclusion the counterparty may claim.
3. Derived series: running accumulators, remaining allowance, projected year-end result.

Flag anything that must be **captured before commissioning**; retroactive shadow calculation is impossible.

## Measurement convention (proposed, pending counterparty agreement)

*Include this section whenever the contract states what is judged but not how to measure it* (how "unavailable capacity" is quantified intra-event, how a power set-point stream converts to an hourly energy benchmark, which signal is the capability reference). Silent methodology is a future dispute; the party that writes the convention first, in a document the other side reviews on a clock, usually keeps it.

- State the convention as computable rules (a small formula block), with each choice justified: which signal, which overlay (measured series vs event/ticket ledger, and which wins), the reference denominator, any materiality threshold, and the treatment of gaps and invalid instructions.
- Prefer signals **traceable to the counterparty's own measurement** where one exists (the vendor's availability signal, the meter both contracts share): a convention built on the other side's number is hard for them to attack.
- Name the **vehicle for agreement** (typically the operating-procedures draft or an agreed report form) and the deadline (usually pre-COD; retroactive conventions are negotiated in a dispute).
- Record what the convention deliberately excludes (e.g. energy-state effects kept out of an availability derate) and which other mechanism covers it.

## Pseudo-SQL

> Illustrative only: placeholder table and column names, generic SQL dialect, not runnable. Real names come from the historian design and the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md). **Where this disagrees with the formula above, the formula wins.** All sheets in this folder use the same placeholder schema (see the [folder index](index.md)).

Worked example: event-based availability, the most common shape:

```sql
WITH p AS (
    SELECT MAX(CASE WHEN param_key = 'contract_capacity_mw' THEN param_value_num END) AS power_capacity_mw,
           MAX(CASE WHEN param_key = 'planned_outage_cap_h' THEN param_value_num END) AS planned_cap_h
    FROM contract_param
),
-- Equivalent outage hours: duration x (unavailable MW / plant MW).
equiv AS (
    SELECT -- an outage noticed too late is FORCED regardless of intent
           CASE WHEN e.classification = 'PLANNED' AND e.notice_business_days < :required_notice_bd
                THEN 'FORCED' ELSE e.classification END AS effective_class,
           (EXTRACT(EPOCH FROM (e.end_ts - e.start_ts)) / 3600.0)
               * (e.unavailable_mw / p.power_capacity_mw) AS equiv_h
    FROM outage_event e CROSS JOIN p
    WHERE e.start_ts >= :period_start AND e.start_ts < :period_end
),
totals AS (
    SELECT SUM(CASE WHEN effective_class = 'FORCED'  THEN equiv_h ELSE 0 END) AS tf_raw,
           SUM(CASE WHEN effective_class = 'PLANNED' THEN equiv_h ELSE 0 END) AS tp_raw,
           SUM(CASE WHEN effective_class = 'FM'      THEN equiv_h ELSE 0 END) AS fm,
           SUM(CASE WHEN effective_class = 'TEST'    THEN equiv_h ELSE 0 END) AS test_h
    FROM equiv
)
SELECT ( :hours_in_period - (tf_raw + GREATEST(tp_raw - p.planned_cap_h, 0))   -- cap overflow -> forced
                          - (LEAST(tp_raw, p.planned_cap_h) + test_h) - fm )
     / NULLIF( :hours_in_period - (LEAST(tp_raw, p.planned_cap_h) + test_h) - fm, 0)
       AS availability
FROM totals CROSS JOIN p;
```

**Easy to get wrong:** *(one short paragraph per sheet, tying the SQL back to this guarantee's trap, e.g. that planned hours beyond the cap move into the forced bucket rather than simply losing their exclusion, and that no telemetry table appears here at all because the calculation reads the event log.)*

## Reporting and verification

Who reports, when, in what format, and the review/dispute clock. Note any **deemed acceptance**: silence that ratifies a counterparty number is a deadline, not a formality.

## LD (reference)

Rate, cap, and accrual mechanics. Recorded for reference: the commercial terms are settled, and the engineering payload is the methodology above.

## Open items

Documents not held, methodologies the contract defers, conventions to agree with the counterparty before the first assessment.
