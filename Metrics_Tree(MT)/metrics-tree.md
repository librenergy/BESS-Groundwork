---
type: Template
title: Metrics Tree
description: "The project's metrics layer: the metrics tree (everything that affects revenue, grouped by the agreement that governs it) and the per-segment metric registry with codes (PG-xxx contractual, MT_ owner). Calculation sheets live in calculations/; targets, sources, and reconciliation live in each metric's one home."
---

# Metrics Tree

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Source ONT version:** `{{ONT_VERSION}}`

> **One document, two things.** The **tree** is the map: everything that affects revenue, grouped by the agreement that governs it, down to what a performance engineer monitors. The **per-segment metric registry** below it lists every metric with its code and its one definition home. Nothing else lives here: targets for performance-guarantee metrics are in the [PGM](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md), owner-metric thresholds and sources of truth are in each metric's calculation sheet, and event-tagging process is the [Outage Tracker](../Data_Product%28DP%29/Outage_Tracker/outage-tracker.md)'s.

> **Everything here is a metric**: a calculated quantity with exactly one authoritative definition (formula, inputs and their sources, boundary, clock), so the platform, the vendor, the offtaker, and the monthly report produce the *same number*. A target is a property a metric carries in its definition home, not a separate kind of thing. Where a quantity carries **two contractual definitions** (availability and RTE routinely differ between the offtake agreement and the service agreement), define **two metrics, one per contract**, each with its own sheet and reporting chain. Never merge them.
>
> **Metric identity and homes.** Performance-guarantee metrics carry their **PG-xxx** ID (three digits, series by counterparty, per the [PGM](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) header) and are defined in the PGM and its calc sheets. Owner metrics (engineering + monitoring) carry **`MT_<SEG_TYPE>_<NNN>`** codes (segment type = the grain the metric reports at, three digits, append-only per series) and are defined in [calculations/](calculations/index.md). Both schemes are registered in the [ontology's taxonomy](../Ontology%28ONT%29/taxonomy.md). **One definition home per metric; every other appearance is a link.**

> ### 🔗 View this diagram online
>
> A published, view-only copy is easier to read and pan than the block below:
>
> **[BESS-Groundwork Metric Tree on Mermaid.ai](https://mermaid.ai/d/c658a0fc-b68f-4dd5-a42d-1168ffb6086d)**
>
> **The diagram in this file is the source of truth.** The online copy is a convenience, not the record:
>
> - **It drifts.** Edit either copy and they diverge. Re-sync the block below after editing online, and re-fetch the online copy before editing it, because concurrent edits overwrite rather than merge.
> - **Access is not guaranteed.** Publishing depends on a subscription that may lapse, so the link may stop working. Nothing in this repo may depend on it, which is why the block below is the record.
>
> *Working in a clone? Replace the link with your own diagram, or delete this callout.*

## What the tree is

A single map of **everything that affects revenue**, so that each box on it can be monitored. It shows how faults and shortfalls roll up through the four availability types into the contractual numbers that move money.

It is deliberately shaped around **what a performance engineer monitors**, not around a generic revenue model. It stops at the metric level on purpose: **where each number comes from is not a question for this tree.** Systems, tags and interfaces are settled later, in the [Data Interface Register](../Data_Interface_Register%28DIR%29/data-interface-register.md), and putting them here only crowds out the thing the tree is for.

**What it is for:**

- **Root-causing a headline miss.** Walk down from the LD or the guarantee, into the availability type responsible, to the fault or signal underneath.
- **Seeing both sides of the same event.** The same outage feeds two contracts with different formulas, clocks, and verdicts. The tree shows both paths and refuses to link them.
- **Setting the monitoring scope.** Every box is something to watch. If a box has no monitoring behind it, that is the gap.
- **Scoping dashboards and the monthly report.** The tree is the menu; pick the nodes each audience needs.

## How to use it

1. Copy this folder for your project (do not edit the base template).
2. Run [`eim-review-build`](../Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) first. The tree references EIM node IDs, and the [Data Interface Register](../Data_Interface_Register%28DIR%29/data-interface-register.md) and [Performance Guarantee Matrix](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) supply source references and contractual definitions.
3. **Keep the PG-NN references in step with the [PGM](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md).** Each LD and guarantee node carries its guarantee ID so the two documents tie together; levels, rates and clocks live in the PGM, never here. **This tree is the source of truth for what gets monitored**, so every node must have a PGM row. PGM §1.3 holds the mapping and lists the rows deliberately not drawn here.
4. Prune or extend to match the actual contract stack. A merchant asset replaces the deductions side with market streams; a project with no service agreement loses the recovery side entirely.
5. Fill the node-definition table and the per-segment registry; write one calculation sheet per owner metric in [calculations/](calculations/index.md). The **sheets** are where each number gets its inputs, sources of truth, boundary, clock and target; the tree deliberately does not carry them.
6. Re-stamp `{{EIM_VERSION}}` whenever the source EIM changes.

## The tree

> **Reading it.** Read down: money, then the guarantee or deduction, then how it is measured, then what sits underneath. The two boxes are the two agreements. Purple nodes resolve to a metric definition below; dashed-grey nodes are what you look at when the purple one moves.

```mermaid
---
config:
  layout: elk
---
flowchart TB
 subgraph OFFTAKE["OFFTAKE — PPA / Tolling Agreement<br>what we are paid, and what they take off"]
    direction TB
        PAYM["Market or Tolling Payments"]
        LOSE["Offtake deductions<br>or LD"]
        AVLD["Availability LD<br>PG-101"]
        DISLD["Dispatch adjustment<br>PG-104"]
        CAPLD["Capacity and RTE adjustment<br>PG-102 · PG-103"]
  end
 subgraph LTSAP["VENDOR — Long-Term Service Agreement (LTSA)<br>what we claim back, and how we prove it"]
    direction TB
        RECOV["LTSA and Vendor LD Recovery"]
        PAGUAR["Power availability guarantee<br>PG-201"]
        CAPGUAR["Capacity guarantee<br>PG-202"]
        CLAIM["How we get paid"]
        VENDREP["Their number<br>the OEM's own report"]
        SHADOW["❓MT · Our number [PE]<br>shadow copy, set up before year one"]
        DELTA["❓MT · The difference<br>backed by evidence [PE]"]
        OBE["❓MT · OBE — how we measure it<br>the weakest of the four, every interval"]
        EA["❓MT · Equipment Availability<br>is the equipment on?"]
        OA["❓MT · Operational Availability<br>did we follow the setpoint?"]
        PA["❓MT · Power Availability<br>can we deliver full power?"]
        QA["❓MT · Energy Availability<br>do we still hold the energy?"]
        EABAT["Battery and string faults<br>contactor open, BMS says unavailable"]
        EAPCS["PCS faults<br>module status, isolation"]
        EABOP["BOP and grid<br>transformers, protection, comms"]
        PACP["Charge and discharge power<br>ACP and ADP per string and PCS"]
        PDER["Why it derated<br>SOC imbalance, temperature, voltage, current limit"]
        OSET["Setpoint vs actual<br>shortfall outside the dead-band"]
        QTEST["Capacity test<br>tested energy vs guaranteed energy<br>(once or twice a year)"]
  end
    LOSE --> AVLD & DISLD & CAPLD
    AVLD --> EA
    RECOV --> PAGUAR & CAPGUAR & CLAIM
    CLAIM --> VENDREP & SHADOW
    SHADOW --> DELTA
    PAGUAR --> OBE
    CAPGUAR --> QA
    OBE --> EA & OA & PA & QA
    EA --> EABAT & EAPCS & EABOP
    PA --> PACP & PDER
    OA --> OSET
    QA --> QTEST
    ROOT["Performance Revenue"] --> PAYM
    ROOT == − lose === LOSE
    ROOT == + recover === RECOV
    DISLD --> OA
    CAPLD --> QTEST
    AVLD -- excused under the offtake? --> OUTLOG["CENTRAL OUTAGE LOG [OP · PE]<br>every grid outage card, scheduled maintenance, large derate<br>(similar to Solar GADS)"]
    SHADOW -- an excuse event for the vendor? --> OUTLOG

     PAYM:::Pine
     LOSE:::Rose
     AVLD:::pg
     DISLD:::pg
     CAPLD:::pg
     RECOV:::Aqua
     PAGUAR:::pg
     CAPGUAR:::pg
     CLAIM:::driver
     VENDREP:::signal
     SHADOW:::mtc
     DELTA:::mtc
     OBE:::obe
     EA:::mtc
     OA:::mtc
     PA:::mtc
     QA:::mtc
     EABAT:::signal
     EAPCS:::signal
     EABOP:::signal
     PACP:::signal
     PDER:::signal
     OSET:::signal
     QTEST:::signal
     ROOT:::root
     OUTLOG:::central
    classDef root fill:#0f172a,stroke:#0f172a,color:#ffffff,font-weight:bold
    classDef obe fill:#7c3aed,stroke:#4c1d95,color:#ffffff,font-weight:bold
    classDef central fill:#334155,stroke:#0f172a,stroke-width:3px,color:#ffffff,font-weight:bold
    classDef driver fill:#e2e8f0,stroke:#94a3b8,color:#0f172a
    classDef pg fill:#fef3c7,stroke:#b45309,stroke-width:2px,color:#78350f,font-weight:bold
    classDef mt fill:#ede9fe,stroke:#7c3aed,stroke-width:2px,color:#4c1d95,font-weight:bold
    classDef mtc fill:#faf5ff,stroke:#a78bfa,stroke-dasharray:4 3,color:#5b21b6
    classDef signal fill:#f1f5f9,stroke:#cbd5e1,color:#334155,stroke-dasharray:3 3
    classDef Aqua stroke-width:1px, stroke-dasharray:none, stroke:#46EDC8, fill:#DEFFF8, color:#378E7A
    classDef Pine stroke-width:1px, stroke-dasharray:none, stroke:#254336, fill:#27654A, color:#FFFFFF
    classDef Rose stroke-width:1px, stroke-dasharray:none, stroke:#FF5978, fill:#FFDFE5, color:#8E2236
    style OFFTAKE fill:#f0fdf4,stroke:#27654A,stroke-width:2px
    style LTSAP fill:#f0fdfa,stroke:#0e7490,stroke-width:2px
```

## Reading the tree

### Two agreements, two boxes

The tree is grouped by **the contract that governs the money**, because that is the boundary that decides which formula applies, which clock it runs on, and who you argue with.

**Box 1: OFFTAKE, the PPA or tolling agreement.** What we are paid and what they take off. The market or tolling payment comes in; the deductions come off it, one per contractual mechanism: availability, dispatch, capacity and RTE.

**Box 2: VENDOR, the long-term service agreement.** What we claim back and how we prove it. The guarantees, the measurement behind them, and the claim mechanism. This is the only money the project can actually get **back**, which is why it carries the depth: the offtake side can only ever reduce a payment.

**Between them sits the central outage log**, deliberately outside both.

Anything that does not change a number in one of those two boxes does not belong on the tree. That rule is what keeps it readable, and it is why condition-monitoring items such as temperature envelopes and maintenance schedules are not drawn: they matter operationally and they belong in the [PGM](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) exclusions and the SOPs, but they are not a line in the money walk.

### Inside the LTSA box, in build order

The claim is the point, so it follows the order you would actually build it:

1. **The guarantees.** Usually two worth tracking: a **power availability guarantee** and a **capacity guarantee**.
2. **How we measure them.** The power availability guarantee resolves into **OBE**, the four availability types. The capacity guarantee resolves into energy availability and its test.
3. **How that becomes money.** Their number, our number beside it, the difference with its evidence. Without our own number there is no difference to show, and without a difference there is no claim.

| | |
|---|---|
| **EA** Equipment Availability | Is the equipment on? |
| **OA** Operational Availability | Did we follow the setpoint? |
| **PA** Power Availability | Can we deliver full power right now? |
| **QA** Energy Availability | Do we still hold the energy we were promised? |

`OBE = the weakest of the four, averaged over every interval`. The full method is in the [Daily Performance Report §2](../Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md); the tree just shows where each one connects to money.

### The central outage log

One log, every event: every outage card raised with the grid operator, every trip, every derate, every piece of major downtime. It sits **outside both boxes on purpose**, because it belongs to neither agreement and is read by both.

The two edges into it are the two questions asked of the same event:

- **Excused under the offtake?** If not, it counts against the availability LD.
- **An excuse event for the vendor?** If so, it comes out of their denominator and they are not charged for it.

**One event, judged twice, and the two verdicts routinely disagree.** A comms failure or a BOP outage is typically the owner's problem under the offtake agreement and excused for the vendor, which is exactly the gap the log exists to make visible. Notice discipline lives here too: a planned outage without its notice on time usually converts to forced, which is a money event created by paperwork rather than by the plant.

The taxonomy is **GADS-aligned**, closest to how Solar GADS handles co-located storage today, since no standalone BESS GADS exists yet. Building it that way from day one means the log exports rather than gets rebuilt when NERC opens BESS reporting. The full event set, cause codes and per-contract verdict fields are specified in the [Outage Tracker](../Data_Product%28DP%29/Outage_Tracker/outage-tracker.md); this node is where it connects to the money.

### The lines that cross between the boxes

Three edges leave the offtake box and land in the LTSA one, and they are the most important thing on the diagram:

- **Availability LD → Equipment Availability**
- **Dispatch adjustment → Operational Availability**
- **Capacity and RTE adjustment → the capacity test**

The offtake side needs only two of the four measures, and it uses **the same nodes**, not copies. So one measurement serves both agreements even though the two contracts calculate, weight and excuse it completely differently. That is the whole argument for measuring the plant once, properly, instead of once per contract.

The two agreements are otherwise drawn with **no line between them**. Different formulas, boundaries, clocks and LD pools, settled separately. Linking them would invite exactly the reconciliation mistake the rest of the toolkit warns about.

**The tree stops at the metric level.** Which system, tag or interface supplies each number is a separate question, answered in the [Data Interface Register](../Data_Interface_Register%28DIR%29/data-interface-register.md) and in each metric's calculation sheet. Keeping it out is what lets the diagram stay a map of the money rather than a map of the plumbing.

### Node taxonomy

| Class | Style | Meaning |
|-------|-------|---------|
| **Root** | dark | The money walk |
| **Agreement box** | green (offtake) / teal (vendor) | The contract that governs the money inside it |
| **Central log** | slate, heavy border | Shared by both agreements, owned by neither |
| **Performance guarantee** | amber, solid border | A contractual instrument (`PG-xxx`): owed to the offtaker or held from a vendor. Defined in the [PGM](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) and its calc sheet |
| **Owner metric** | purple, solid border | An owner-computed metric with an assigned `MT_` code, shadow calculations included. Defined in [calculations/](calculations/index.md). **OBE** is drawn as filled purple because it is the composite the other four roll into |
| **Owner-metric candidate** | purple, dashed border, `❓MT` | Registry candidate: the owner will compute it, the code is assigned when the metric is actually built |
| **Signal** | dashed grey | Raw or derived quantity, not yet a coded metric |

**Colour encodes metric class, not position on the tree**, and it answers one question at a glance: **amber means someone owes money on this number** (contractual, disputable, LD-bearing); **purple means the owner computes it for itself** (evidence, early warning, or a shadow of an amber number). A shadow calculation is always purple, never amber: replicating the vendor's availability guarantee does not make the replica a guarantee. Promoting a candidate to a built metric is a recolour (dashed → solid) plus a code.

### Owner tags

`[PE]` performance engineering, `[AM]` asset manager, `[OP]` operator, `[OEM]` service provider. The performance engineer is deliberately in focus: PE owns the shadow number, the difference and its evidence, the OBE measures, the outage-log QA, and the usage counters. Fill the rest from the RACI session.

## Node-definition table

Fill one row per node. `Metric ref` points to a metric definition sheet below; `Source / DIR ref` points to the interface that produces it. Leave `❓` + owner + date where unknown.

| Node ID | Node | Side | Type | Metric ref | Source / DIR ref | How it is worked out |
|---------|------|------|------|------------|------------------|---------------|
| ROOT | Performance Revenue | — | Outcome | — | derived | payments − deductions + recovery |
| PAYM | Market or Tolling Payments | offtake | Driver | — | settlement | capacity payment or market margin |
| RECOV | Vendor LD Recovery | recover | Head | — | derived | Σ guarantee claims, capped |
| PAGUAR | Power availability guarantee | vendor | Metric | PG-201 | PGM calc sheet | (guarantee − actual) × rate |
| CAPGUAR | Capacity guarantee | vendor | Metric | PG-202 | PGM calc sheet | per shortfall formula |
| CLAIM | How we get paid | recover | Driver | — | process | check, dispute, invoice |
| VENDREP | Their number | recover | Signal | — | OEM report | as published by the OEM |
| SHADOW | Our number | recover | Metric | — | owner-held signals | our replica of their definition |
| DELTA | The difference | recover | Metric | — | derived | ours vs theirs, with evidence |
| LOSE | Offtake deductions | lose | Head | — | settlement | Σ of the deductions below |
| AVLD | Availability LD | offtake | Metric | PG-101 | PGM calc sheet | (guarantee − actual) × rate |
| DISLD | Dispatch adjustment | offtake | Metric | PG-104 | PGM calc sheet | per settlement mechanism |
| CAPLD | Capacity and RTE adjustment | offtake | Metric | PG-102 · PG-103 | PGM calc sheet | per adjustment tables |
| OUTLOG | Central outage log | both | Central | — | Outage Tracker | every event, with a verdict per contract |
| OBE | OBE, how we measure it | both | OBE | OBE | derived | weakest of the four, each interval |
| EA | Equipment Availability | both | Metric | EA | BMS + PCS status | min at string, min at bus, Σ to site |
| OA | Operational Availability | both | Metric | OA | meter + dispatch | intervals inside the dead-band |
| PA | Power Availability | recover | Metric | PA | BMS + PCS | rail-aware min(charge, discharge) |
| QA | Energy Availability | recover | Metric | QA | capacity test | tested energy ÷ guaranteed energy |
| EABAT | Battery and string faults | measure | Signal | ← EA | BMS | contactor state and BMS availability, per string |
| EAPCS | PCS faults | measure | Signal | ← EA | site controller | module status and isolation |
| EABOP | BOP and grid | measure | Signal | ← EA | site controller | transformers, protection, comms |
| PACP | Charge and discharge power | measure | Signal | ← PA | BMS + PCS | ACP and ADP per string and module |
| PDER | Why it derated | measure | Signal | ← PA | site controller | imbalance, temperature, voltage, current limit |
| OSET | Setpoint vs actual | measure | Signal | ← OA | meter + dispatch | shortfall outside the dead-band |
| QTEST | Capacity test | measure | Signal | ← QA | revenue meter | tested vs guaranteed energy |

## Core metrics by segment (starter registry)

Metrics are organized down the segment hierarchy (per the [ontology's segment taxonomy](../Ontology%28ONT%29/segment-types.md)): a metric sits at the segment whose performance it characterizes; its measurement boundary is stated in its sheet. **This document defines nothing**: PG-xxx rows resolve to their [PGM calc sheets](../Performance_Guarantee_Matrix%28PGM%29/calculations/index.md), MT_ rows to [calculations/](calculations/index.md), one home per metric. Codes are assigned when a metric is actually built (the [ontology fill loop](../Ontology%28ONT%29/ontology.md)), never speculatively; unbuilt starter rows stay `❓`. The **Related PG** column types each owner metric's guarantee relationship: `itself` (PG rows) / `shadows` / `evidences` / `feeds` / `leads`. Prune or extend per the project's contracts; segment sections with nothing yet stay as scaffolds.

### Site

| Code | Metric | One line | Related PG | Home |
|:---|:---|:---|:---|:---|
| ❓ | Excused-event log quality and notice compliance | events attributed + evidenced within the contract windows; notices sent on every noticeable event | evidence duty for both availability PGs | candidate (process: Outage Tracker) |
| ❓ | Availability reconciliation delta | owner vs vendor vs offtaker views, monthly, deltas documented and contested in-window | feeds both availability PGs | candidate |
| ❓ | LD accrual vs recovery ledger · outage budget burn-down | financial and allowance roll-ups for the monthly report | — | candidates |

### POM: the commercial boundary (revenue meter)

| Code | Metric | One line | Related PG | Home |
|:---|:---|:---|:---|:---|
| PG-1xx | Availability, offtake definition | per the offtake agreement's formula and clock | itself | PGM calc sheet |
| PG-1xx | Capacity / RTE / dispatch, offtake definitions | one metric per contractual mechanism | itself | PGM calc sheets |
| ❓ | Offtake cycles & throughput vs usage rights | ⚠️ the offtake and vendor agreements typically define cycles on **different denominators and clocks** (contracted energy vs guaranteed/tested capacity; contract year vs operating year): **two metrics, never merged**, like the two availabilities | offtake usage limits | candidate |
| ❓ | Vendor EFC vs limitation tables | the counter that ends things: runway projection vs warranty and guarantee termination cliffs, on the **vendor's** EFC convention | gate for the vendor PGs | candidate |
| ❓ | Auxiliary load | house loads, % of throughput; depresses RTE and tested capacity | feeds RTE PGs | candidate |

### BESS: the system

| Code | Metric | One line | Related PG | Home |
|:---|:---|:---|:---|:---|
| PG-2xx | Availability, service-agreement definition | per the LTSA's formula and clock; never merged with the offtake one | itself | PGM calc sheet |
| PG-2xx | Capacity retention / vendor RTE | test-based vendor guarantees | itself | PGM calc sheets |
| ❓ MT_BESS_ | Equipment Availability (EA) | is the equipment on? | evidences both availability PGs | sheet on build |
| ❓ MT_BESS_ | Operational Availability (OA) | did we follow the setpoint? | evidences dispatch PG | sheet on build |
| ❓ MT_BESS_ | Power Availability (PA) | can we deliver full power right now? (rail-aware `min`) | evidences vendor availability | sheet on build |
| ❓ MT_BESS_ | Energy Availability (QA) | do we still hold the energy promised? | evidences retention PG | sheet on build |
| ❓ MT_BESS_ | OBE composite | weakest of the four, per interval | evidences | sheet on build |
| ❓ MT_BESS_ | Shadow-vs-vendor availability delta | owner replica vs the vendor's self-assessment, with evidence | shadows the vendor availability PG | sheet on build (before year one) |
| ❓ MT_BESS_ | Telemetry validity vs minimum intervals | valid intervals per period vs the contractual validity floor | feeds the vendor availability PG | candidate |

### Unit level (per the project's leaf segment: AC block / battery string / integrated unit)

| Code | Metric | One line | Related PG | Home |
|:---|:---|:---|:---|:---|
| ❓ | SOC imbalance / string spread | leading indicator of derates and degradation | leads retention PG | candidate |
| ❓ | Per-unit capability / fault decomposition | diagnostic decomposition of the BESS-level availability metrics | — | candidate |

## Calculation sheets

One standalone sheet per **owner metric** in [calculations/](calculations/index.md), mirroring the PGM's pattern ([sheet template](calculations/metric-sheet-template.md)). **Each sheet is self-contained**: its inputs declare the **source of truth** for every quantity it reads (which system wins when several report it, and why), its target/alert threshold if it carries one, and its reconciliation/plausibility checks. There is no central source-of-truth or reconciliation table: those decisions live with the metric they serve, and the classic rules travel with the sheet template (meter for money, the controlled test over telemetry, never verifying a counterparty's number with the counterparty's data, per-contract clocks).

## Customization notes

- **Merchant assets replace the deductions side.** Swap the offtake LDs for the market streams (energy arbitrage, ancillary services, capacity), keeping the same discipline: each stream ends in a measured quantity, not a revenue estimate. The recovery side and the OBE measures are unchanged, because the plant is the same plant.
- **No service agreement means no recovery side**, but keep the OBE measures. Without a vendor to claim against they are still how the plant is run and how the offtake deductions get explained.
- **Resist adding conditions.** Temperature envelopes, maintenance schedules, access duties and telemetry validity all matter, but they are excuse-event mechanics and belong in the [PGM](../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) and the SOPs. Putting them here is what turns a money tree back into an unreadable map of everything.
- **Promote signals to coded metrics** as instrumentation matures: when a signal gets a contractual envelope or a dashboard threshold, assign its `MT_<SEG_TYPE>_<NNN>` code, write its calculation sheet, and recolor it from dashed grey to dashed purple (candidate) or solid purple (built).
- **Keep node IDs stable.** The node-definition table, dashboards, and the Monthly Performance Report reference them; renaming an ID is a breaking change.
- **Every metric node carries its reference** in the label: `PG-xxx` for guarantees, the `MT_` code for built owner metrics, `❓MT` for registry candidates (codes are append-only and never assigned speculatively).
- **No source systems in the tree.** The tree shows performance-guarantee items and metrics only; where each quantity comes from is answered by the metric's calculation sheet (the one home for inputs and sources of truth) and the [Data Interface Register](../Data_Interface_Register%28DIR%29/data-interface-register.md), not by the diagram.
- **Keep tree and definitions consistent.** Every purple node must resolve to a registry row (and a calculation sheet once built). A purple node with no registry row is a gap to close in-session.

## Open items

Tracked in this folder's `todo.md` (create it with the first item).
