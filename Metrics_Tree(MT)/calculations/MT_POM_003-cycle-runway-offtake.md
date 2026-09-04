---
type: Template
title: "MT_POM_003 — Cycle runway, offtake convention"
description: "Owner metric: offtake cycles and throughput used against the usage rights, on the offtake agreement's own denominator and clock, with the projection to year end. Never merged with the vendor's EFC counter."
---

# `MT_POM_003` — Cycle runway, offtake convention

**Class:** monitoring · **Segment grain:** `POM` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** How much of the **offtake agreement's usage right** has been consumed, and when it runs out at the current rate. Counted the way that contract counts it, on its clock. It is **not** the vendor's equivalent-full-cycle counter, which uses a different denominator (guaranteed or tested capacity) and a different clock (the operating year) and stays a separate `❓` registry candidate, never merged. It is not the BMS `cycle_count` (the reporting system's own convention), and not a guarantee: a usage right is a limit the owner manages, not a number a counterparty tests.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_POM_003` |
| **Formula** | `EFC_offtake(period) = Σ energy_exported at the POM / contracted_energy` (❓ or exactly as the offtake defines a cycle: export only, or export and import over two, or a throughput cap in MWh; clause `{{CLAUSE}}`), accumulated from the start of the offtake's year; `runway = allowed_cycles − EFC_used`; `days_to_limit = runway / trailing_{{30}}_day_rate`; `projected_year_end = EFC_used + rate × days_remaining`. **Gaps:** a meter gap is `null` for the day and the accumulator carries the last confirmed value with a flag; never filled from PCS data, because the offtaker will settle on the meter. |
| **Units** | cycles (or MWh where the right is a throughput cap); days; cycles per day |
| **Measurement boundary** | the POM revenue meter, the boundary the offtake settles on |
| **Inputs & sources of truth** | `energy_exported` (and `energy_imported` if the definition uses it) from the **revenue meter**; `contracted_energy` on the offtake's **schedule** for the current contract year; the usage-rights clause (`{{CLAUSE}}`, ❓ whether a fee or a cap applies above the limit) |
| **Secondary / cross-check source** | the offtaker's own monthly statement of cycles, reconciled monthly (a delta is a definition disagreement to settle in writing, not a data error) |
| **Calculation interval** | daily accumulator; monthly reconciliation |
| **Aggregation** | daily → cumulative on the offtake's contract year; reset on the contract-year boundary, never on 1 January unless they coincide |
| **Timezone & clock convention** | the **offtake's contract year** and the settlement timezone; the vendor's operating year is a different clock and lives on the vendor counter |
| **Exclusions** | per the offtake: ❓ whether test energy or offtaker-instructed excess cycling is excluded from the count |
| **Target / alert** | held against the usage right: alert when `projected_year_end > allowed × {{0.9}}`, and when `days_to_limit < {{n}}`. Review weekly; the dispatch optimiser needs this number, not a monthly report |
| **Related PG** | none directly (a usage right, not a guarantee); interacts with `PG_102` and `PG_202` through degradation |
| **Owner of the calculation** | `[PE]` computes; `[AM]` owns the decision when the runway shortens |
| **Reporting chain** | dashboards (daily), Monthly Performance Report; the offtaker exchange (counterparty statement reconciliation) |
| **Reconciliation / plausibility checks** | reconciles to the offtaker's statement within `{{x}}` cycles per month; `EFC_used` monotonic within a contract year; the vendor counter is reported **beside** this one on its own convention, and the two are never added or compared as if equal |
| **Known discrepancies** | The vendor EFC reads **differently by design**: denominator (guaranteed or tested capacity, which decays) and clock (operating year) both differ, so the vendor counter runs faster as the plant ages. The BMS `cycle_count` is a third convention. Three counters, three conventions, stated every time they appear together |

## Easy to get wrong

Merging the offtake and vendor counters, or normalising one to the other. Resetting on the calendar year. Counting from PCS or EMS energy when the offtaker settles on the meter. Not reading the clause: whether a cycle is export-only changes the count by a factor of two.
