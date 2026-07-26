---
name: bess-gads
title: BESS GADS — Outage & Availability Event Accounting — Facilitated Session
type: Skill
description: Build or update the project's BESS GADS document — the GADS-aligned outage/derate event taxonomy that feeds every contractual availability calculation. Use whenever the user wants to define or tag outage categories and event types, reconcile availability records between the OEM CMMS, the offtaker/ISO view, and the data platform, set partial-outage/derate accounting rules, prepare or contest availability LD calculations, or get ready for NERC GADS reporting. Requires the project EIM, Metrics & KPIs (availability formulas), and the Performance Guarantee Matrix (availability guarantees) — run those skills first if missing.
---

# BESS GADS — Outage & Availability Event Accounting — Facilitated Session

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Template: `BESS_GADS(GADS)/bess-gads.md`. The premise: NERC GADS is the industry's event-accounting standard (Conventional, Wind, Solar — standalone BESS pending, storage today only as co-located groups in GADS Solar), so the project's event taxonomy is **GADS-aligned from day one** — one authoritative event record, tagged once, feeding every contractual availability calculation, exportable when NERC opens BESS reporting. The availability *formulas* live in `Metrics_Tree(MT)/metrics-and-kpis.md`; this document owns the **event record** they depend on.

## Prerequisites

- Project EIM (identifies the ledgers and who writes them: GOP → offtaker/OMS notices; OEM → CMMS; telemetry → owner platform).
- Metrics & KPIs for the availability formula variants; Performance Guarantee Matrix for which guarantee each variant serves. Capture ad hoc if missing, and flag.

## Session flow

1. **Event types.** Walk the GADS-aligned set (PO/PE/MO/U1–U3/SF/RS/NC/D1–D4/PD + BESS-specific EL and AUG): confirm each type's contractual mapping — especially the traps: MO may have no offtake analog (insufficient notice → forced), RS/idle-healthy counts *available*, EL (energy-limited) is usually not an outage under capability-based availability, AUG needs its cap treatment negotiated before the first campaign.
2. **Cause codes.** Confirm the families (BAT/PCS/BOP/CTL/GRD/FM/OWN/TST) against the project's actual equipment (integrated PCS collapses into BAT) and enumerate component subcodes with the O&M/controls vendors. ⚠️ FM definitions differ per contract — tag which definition(s) an event satisfies.
3. **Per-contract verdicts.** For each event, each contract rules independently (offtake: forced/planned/FM/test/no-impact; OEM: counts/excuse/planned-allowance/invisible/hour-deleted). Agree the notice workflows (planned-outage notice; forced-outage notice deadlines) and the deferability-assessment discipline — these are the LD-avoidance and reclassification levers.
4. **Capacity accounting rules.** Rated vs tested basis per contract; MW-weighting interval; the derate detection threshold (X MW for Y minutes, from which signal); **charge and discharge capability tracked separately** — OEM availability rules based on `max(charge, discharge)` capability make one-sided derates invisible while the offtaker may still care.
5. **Ledger reconciliation.** Who writes each ledger, monthly matching procedure, timestamp tolerance, and the disposition of each flag type (unlogged outage, paper outage, verdict mismatch). The owner event log is the authoritative record.
6. **Backfill test.** Run last month's real events through the draft taxonomy live — nothing exposes a broken category set faster.

## Facilitation guidance

- The OEM categorizes generously in its own favour; the counter is *evidence, not argument* — anchor every category on required contemporaneous evidence (timestamps, cause, MW effect, logged within days).
- Derates are where availability quietly leaks. If the project only tracks full outages, flag derate detection as platform work.
- An unlogged event is invisible to event-based availability formulas — log discipline *is* the control; make someone own log QA.
- Ask who arbitrates a disputed verdict and within what window — write it down.

## Outputs

1. `BESS_GADS(GADS)/bess-gads.md` populated in place (version bumped); folder `todo.md`/`log.md` updated.
2. The backfill test results (last month reconciled) as a worked example.
3. Automation backlog: matching script, derate detector, per-contract verdict engine, monthly reconciliation report, NERC-export mapping (parked until a BESS DRI exists).
