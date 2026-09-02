---
name: metrics-tree
title: Metrics Tree — Facilitated Session
type: Skill
description: Build or update the project's metrics layer: Metrics_Tree(MT)/metrics-tree.md (the tree: how value decomposes to what a performance engineer monitors, plus the per-segment metric registry) and its calculations/ sheets (one per owner metric). Use whenever the user wants to build or prune the metrics tree, define availability/capacity/RTE/dispatch metrics, resolve disagreements between systems reporting different numbers for the same quantity (APM vs OEM vs offtaker), assign metric codes, or set targets and dashboards. Requires the project EIM and ideally the Ontology, Data Interface Register, and Performance Guarantee Matrix (run those skills first if missing).
---

# Metrics Tree: Facilitated Session

> **Before you start:** read `Definitions(DEF)/definitions.md` (what every acronym and term means on this project; ⚠️-flagged entries carry two meanings) and `Ontology(ONT)/taxonomy.md` (canonical names, codes, ID schemes), if populated. Write formulas in the ontology's ratified measurement terms (`Ontology(ONT)/measurements.md`), ratifying missing terms via the `ontology` fill loop as you go. Add any new acronym to the glossary before wrapping up (`definitions-taxonomy` skill).

**One document plus a sheet folder**, built **in order**:

1. **The tree first**: the map of how the headline value decomposes through LDs/adjustments and drivers down to what gets monitored. Cheap to sketch, easy to argue about, and it exposes what the metrics must cover.
2. **Then the per-segment registry**, every metric with its code and its one definition home: **PG-xxx** rows (contractual; defined in the PGM and its calc sheets, referenced here, never restated) and **`MT_<SEG_TYPE>_<NNN>`** rows (owner metrics; defined in `calculations/`, one sheet each). A **Related PG** column types each owner metric's guarantee relationship (shadows / evidences / feeds / leads).
3. **Then the sheets**, one calculation sheet per owner metric actually being built ([template](../../Metrics_Tree%28MT%29/calculations/metric-sheet-template.md)): formula, inputs with their sources of truth, boundary, clock, target/alert, reconciliation checks. Codes are assigned **only when a metric is built**, never speculatively.

The layer is BESS-first but the method is asset-agnostic; for a PV plant the tree's revenue limb decomposes PPA/merchant revenue and the metric set swaps RTE/SOC for PR/soiling/insolation; the structure (tree → registry → sheets) is identical.

## Prerequisites

- Project EIM. Strongly preferred: the Ontology (ratified terms and segment types), Data Interface Register (source references), and Performance Guarantee Matrix (contractual definitions and calc sheets). If missing, run those skills first or capture ad hoc and flag.

## Session flow

### Part 1: The tree

1. **Pick the top node and limbs** to match the revenue model: full toll → fixed payment − LD/adjustment deductions, OEM LD recovery, shallow costs, warranty/guarantee preservation; merchant → market limbs per the template's generic baseline. Prune hard; the tree is the menu, not the encyclopedia.
2. **Drill the money branches to equipment level**: each LD family decomposes through its calculation inputs to the equipment monitoring that explains it. **Every metric node carries its reference**: `PG-xxx` on guarantee nodes, `MT_` codes on built owner metrics, `❓MT` on registry candidates (codes are append-only, never assigned speculatively). **No source-system leaves in the tree**: where the data comes from is answered by each metric's calculation sheet and the Data Interface Register, not the diagram.
3. **Mark edge semantics** (`+ − × ÷ Σ min` on arithmetic edges; dashed = guardrails) and owner tags; cite contract clauses as `%%` comments. Optionally draft in a cloud Mermaid editor (working-copy pattern in the EIM template).

### Part 2: The registry (per segment)

4. **Walk the segment sections** (Site → metering point → BESS → unit level, per the ontology's segment taxonomy): a metric sits at the segment whose performance it characterizes. PG metrics get reference rows pointing at their PGM calc sheets; **never restate a contractual calculation here** (one home per metric). Where availability or RTE carries two contractual definitions, that is two PG rows, never merged — and the same rule applies to **cycle/EFC conventions**: offtake and vendor agreements typically normalize throughput on different denominators and clocks, so count them as two metrics.
5. **List owner metrics as `❓` candidates** until built; type each one's Related PG relationship. The shadow replicas of vendor self-assessments belong here as owner metrics (a shadow is not a guarantee), cross-referenced from the PGM rows they shadow.

### Part 3: The sheets (for metrics being built now)

6. **Assign the code and write the sheet** per the template: formula in ratified terms with explicit null/gap handling; **inputs declaring their sources of truth** (which system wins and why: meter for money, the controlled test over telemetry, never verify a counterparty's number with the counterparty's data); boundary; clock (per-contract clocks never mixed); target/alert if the metric carries one; and its reconciliation/plausibility checks. There is no central source-of-truth or reconciliation table; those decisions live in the sheets.
7. **Tie each metric to implementation**: where is the official number computed (platform model, dashboard, notebook)? Uncomputable metrics go to the automation backlog. Stamp `ONT_VERSION` in the header.

## Facilitation guidance

- Keep the tree and the registry consistent: every purple node must resolve to a registry row (and a sheet once built); a purple node with no row is a gap to close in-session.
- **Colour encodes metric class, not position on the tree**: amber = performance guarantee (`PG-xxx`, defined in the PGM), solid purple = owner metric with an assigned `MT_` code, dashed purple = owner-metric candidate (`❓MT`). The rule to state out loud in-session: amber means someone owes money on this number; purple means the owner computes it for itself. A shadow calculation is always purple — replicating a vendor's guarantee does not make the replica a guarantee, and blurring the two is how shadow numbers get quoted back as if they were contractual.
- When the contractual definition and operational habit differ, record both and mark the contractual one authoritative for money.
- Timezone/interval-convention errors (interval-beginning vs -ending, DST, per-contract evaluation clocks) are the most common silent corruption; ask explicitly.
- Codes are append-only within their series; renaming or renumbering one is a breaking change.

## Outputs

1. `Metrics_Tree(MT)/metrics-tree.md` populated in place (tree + per-segment registry; version bumped, `ONT_VERSION` stamped).
2. One calculation sheet per owner metric built this session, in `calculations/`.
3. Implementation backlog: metrics lacking a source or computation home; folder `todo.md` updated, session logged in the folder `log.md`.
4. Next steps: metrics feed the Monthly Performance Report (data product) and the Outage Tracker directly.
