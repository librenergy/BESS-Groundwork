---
type: Template
title: Outage Tracker — Commercial Outage & Availability Event Accounting
description: "The authoritative record of every commercially significant outage/derate event (ISO/offtaker-reported outages, excused events, planned maintenance) tagged once with a GADS-aligned taxonomy, feeding every contractual availability calculation and ready for NERC GADS storage reporting when it arrives."
---

# Outage Tracker — Commercial Outage & Availability Event Accounting

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}` · **Companions:** [the Metrics Tree](../../Metrics_Tree%28MT%29/metrics-tree.md) (availability formulas) · [PGM](../../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) (guarantees)

> **Scope.** The tracker holds every **commercially significant** event: anything reported to the ISO/offtaker or TOP, anything needing an excused-event verdict under any contract, planned maintenance and grid outages, and any capability loss large enough to move a contractual availability or settlement number. Sub-commercial equipment events (a single unit down with no site-capability impact) stay in the CMMS/observation log; they enter here only as NC rows when worth reconciling.

> **Why the taxonomy is GADS-aligned.** NERC's Generating Availability Data System is the industry's standard event-accounting taxonomy: mandatory for conventional units, extended to wind (2018) and solar (DRI 2024, reporting phased from 2025). **No standalone BESS GADS exists yet**: storage appears today only as *co-located energy-storage groups* inside GADS Solar reporting, and NERC's energy-storage assessment recommends extending battery capture. It is coming. This template keeps the tracker's event taxonomy **GADS-aligned now**, so that (a) every event is tagged once and feeds every contractual availability calculation consistently, and (b) when NERC opens standalone BESS reporting, the log exports to it instead of being rebuilt.

## 1. Event types (GADS-aligned, BESS-adapted)

Every event gets exactly one event type. The GADS column shows the conventional-GADS analog so future NERC export is a mapping, not a migration.

| Code | GADS analog | Definition | BESS adaptation / notes |
|------|-------------|-----------|--------------------------|
| PO | PO: Planned Outage | Scheduled work, noticed per contract (`{{notice period}}`) | Consumes the offtake planned-outage allowance (`{{cap}}` h/yr) and/or the OEM planned allowance |
| PE | PE: Planned Outage Extension | Planned work overrunning its window | Overrun hours risk reclassification as forced |
| MO | MO: Maintenance Outage | Deferrable-but-necessary work scheduled inside the notice window | ⚠️ Many offtake contracts have no MO category: without full notice it counts as forced; use MO for internal truth, map to U-class contractually |
| U1 | U1: Immediate forced outage | Trip / immediate removal, no deferral possible | If the contract has a "could it have been deferred?" test, record the deferability assessment on every U event |
| U2 | U2: Delayed forced outage | Removal required within days | |
| U3 | U3: Postponed forced outage | Deferrable past the weekend but not to a planned window | Prime dispute territory under deferability tests |
| SF | SF: Startup failure | Failed transition into dispatch when commanded | BESS: failed charge→discharge transition, failed wake from standby, failed dispatch-instruction response |
| RS | RS: Reserve shutdown | Healthy but idle | **Counts fully available** under capability-based availability definitions; never log idle-healthy as an outage |
| NC | NC: Non-curtailing | Equipment out with no site-capability impact | Fleet redundancy: unit-level outages that don't reduce site dispatchable power |
| D1–D3 | D1/D2/D3: Forced deratings | Partial capability loss, tiered by deferability like U1–U3 | **Record charge-side and discharge-side MW separately**: availability rules based on `max(charge, discharge)` capability (common in OEM LTSAs) make one-sided derates invisible to the OEM while they may still matter to the offtaker |
| D4 | D4: Maintenance derating | Deferrable partial-capability work | |
| PD | PD: Planned derating | Noticed partial-capability work | |
| EL | — (no GADS analog) | **Energy-limited state**: SOC/throughput-constrained, power capability intact | Usually not an outage under capability-based availability, but log it; it explains dispatch non-delivery and RA-event exposure |
| AUG | — | Augmentation outage | Planned family; negotiate treatment against planned-outage caps *before* the first campaign |

## 2. Cause-code families (BESS)

Second tag: what caused it. Families map to the equipment branches of the [metrics tree](../../Metrics_Tree%28MT%29/metrics-tree.md); enumerate component subcodes per project (GADS-style numeric cause codes).

| Family | Covers | Typical evidence |
|--------|--------|------------------|
| BAT | Battery enclosures / BMS (incl. integrated PCS where applicable) | Site-controller per-unit fault binaries, available-unit counts |
| PCS | Standalone PCS/inverters (skip if integrated → use BAT) | Inverter logs |
| BOP | MV transformers, step-up TX, HV protection, gen-tie, station service | Substation IED/relay records |
| CTL | Controls & comms: site controller, PPC/SCADA, network, incl. lost/failed dispatch commands | Controller flags, PPC logs, network monitoring |
| GRD | Grid/external: grid outage, TOP directive, RAS operation, voltage excursions | TOP correspondence, relay records |
| FM | Force majeure: ⚠️ per-contract definitions differ; tag which definition(s) the event satisfies | Declaration + evidence |
| OWN | Owner-side: access/LOTO delays, software-upgrade delays, owner-directed ops | Owner instructions, access logs |
| TST | Performance tests | Test records; treatment differs per contract and per who requested the test |

## 3. Contract attribution — one event, N independent verdicts

The same physical event is judged separately by each contract. Tag every verdict at event close; never let one imply another.

| Field | Values | Rule |
|-------|--------|------|
| Offtake verdict | forced / planned (within cap+notice) / FM / test-excluded / no-impact | List the causes the offtake contract does **not** excuse (BOP, comms, grid-short-of-FM, owner causes commonly count) |
| OEM/LTSA verdict | counts-against-OEM / excuse event (class) / planned-allowance / invisible (one-sided or masked) / hour-deleted (data gap) | Excuse rows need contemporaneous owner corroboration (timestamps, cause, MW) |
| Notices | planned-outage notice (date, method) · forced-outage notice deadline per contract: sent? timestamp | Timely notices often excuse RA-event LDs and dispatch penalties |
| Deferability | assessed / not-deferrable / deferrable-but-run-forced (why) | The offtaker's reclassification lever where a Prudent-Operating-Practices test exists |

## 4. Event record schema

One row per event in the running log (§8): event ID · start/end timestamps (prevailing time) · event type (§1) · cause family + subcode (§2) · **unavailable MW, charge side** · **unavailable MW, discharge side** · equivalent outage hours (MW-weighted; agree the intra-event MW measurement convention with the offtaker **before COD**) · per-contract verdicts (§3) · notices · deferability · evidence links (CMMS WO, controller flags, relay records, RCA) · disposition.

## 5. Capacity accounting rules

- Basis: `{{rated / tested capacity}}` for offtake equivalent-hours; `{{guaranteed capacity}}` for OEM denominators.
- Partial outages: MW-weighted per interval of `{{INTERVAL}}`; derate detection threshold `{{X}}` MW for `{{Y}}` minutes opens an event (source: `{{signal}}`).
- Track charge and discharge capability separately end-to-end.
- Energy-limited hours (EL) logged but excluded from capability-based availability calculations.

## 6. The ledgers & monthly reconciliation

| Ledger | System (EIM node) | Written by | Used for |
|--------|-------------------|-----------|----------|
| Offtake/market | outage notices / OMS (`OMS`) | GOP / SC | offtake availability settlement basis |
| OEM/contractual | OEM CMMS (`CMMS`) + OEM availability report | OEM | OEM availability & excuse attribution |
| Measured | owner platform / APM (`APM`); **this document's log is the authoritative record** | automated + PE QA | ground truth; every shadow calculation |

Monthly: export all ledgers → match by timestamp overlap (tolerance `{{TOL}}`) → flag unlogged outages, paper outages, verdict mismatches → resolve in the monthly O&M call → publish the reconciled month (feeds the Monthly Performance Report and availability projections).

## 7. Availability calculations

Defined authoritatively in [the Metrics Tree](../../Metrics_Tree%28MT%29/metrics-tree.md): one metric per contract. This document owns the **event record** they depend on; it does not restate formulas.

## 8. Event log (running)

| # | Start | End | Type | Cause | MW chg | MW dis | Eq. hrs | Offtake verdict | OEM verdict | Notices | Evidence | Disposition |
|---|-------|-----|------|-------|--------|--------|---------|------------------|-------------|---------|----------|-------------|
| | | | | | | | | | | | | |

## 9. Disputes & adjustments

| # | Period | Issue | Positions (OEM / offtaker / owner measured) | Resolution | Impact (LD $ / avail %) |
|---|--------|-------|----------------------------------------------|------------|--------------------------|
| | | | | | |

## Open items

Tracked in this folder's `todo.md` (create it with the first item).
