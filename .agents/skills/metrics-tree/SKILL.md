---
name: metrics-tree
title: Metrics Tree & KPIs — Facilitated Session
type: Skill
description: Build or update the project's metrics layer — one document (Metrics_Tree(MT)/metrics-tree.md) holding the metrics tree (how value decomposes to data sources), the authoritative metric calculations, and the KPI target set. Use whenever the user wants to build or prune the metrics tree, define availability/capacity/RTE/dispatch metrics, resolve disagreements between systems reporting different numbers for the same quantity (APM vs OEM vs offtaker), declare authoritative data sources, or set KPI targets and dashboards. Requires the project EIM and ideally the Data Interface Register and Performance Guarantee Matrix (run those skills first if missing).
---

# Metrics Tree & KPIs — Facilitated Session

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

**One document** — `Metrics_Tree(MT)/metrics-tree.md` — holding three layers, built **in order**:

1. **The tree first** — the map: how the headline value decomposes through LDs/adjustments and drivers down to data sources. Cheap to sketch, easy to argue about, and it exposes what the metric definitions must cover.
2. **Then the metric definitions** — one authoritative calculation per metric (formula, boundary, exclusions, clock, reporting chain), in the same document below the tree, so every purple KPI node resolves to a definition without leaving the file.
3. **Then the KPI table** — the slim target set (a **KPI is a metric held against a target** — every KPI is a metric, most metrics are not KPIs).

The layer is BESS-first but the method is asset-agnostic — for a PV plant the tree's revenue limb decomposes PPA/merchant revenue and the metric set swaps RTE/SOC for PR/soiling/insolation; the structure (tree → metrics → targets → sources) is identical.

## Prerequisites

- Project EIM. Strongly preferred: Data Interface Register (source references) and Performance Guarantee Matrix (contractual definitions and calc sheets). If missing, run those skills first or capture ad hoc and flag.

## Session flow

### Part 1 — The tree

1. **Pick the top node and limbs** to match the revenue model: full toll → fixed payment − LD/adjustment deductions, OEM LD recovery, shallow costs, warranty/guarantee preservation; merchant → market limbs per the template's generic baseline. Prune hard — the tree is the menu, not the encyclopedia.
2. **Drill the money branches to equipment level**: each LD family decomposes through its calculation inputs to the equipment monitoring that explains it, ending at blue data-source leaves carrying DIR `SYS/IF` refs.
3. **Mark edge semantics** (`+ − × ÷ Σ min` on arithmetic edges; dashed = guardrails) and owner tags; cite contract clauses as `%%` comments. Optionally draft in a cloud Mermaid editor (working-copy pattern in the EIM template).

### Part 2 — Metrics (the calculations)

4. **One metric per contractual definition.** Availability and RTE routinely differ between the offtake contract and the LTSA — define each **separately** (formula, boundary, exclusions, clock, reporting chain); never merge, and record the delta deliberately. Event tagging lives in BESS GADS; the formulas live here.
5. **Run the sheet per metric.** The hard fields: measurement boundary, authoritative source, aggregation, timezone/clock convention, exclusions, and the **reporting chain** (who produces the official number, the contractual deadline, deemed-acceptance windows, and how the owner's shadow double-checks it). "Availability" without an exclusions list is not a definition.
6. **Source-of-truth decisions.** For each quantity reported by multiple systems (SOC, power, availability, capacity, prices), force an explicit authoritative-hop declaration with rationale; never verify a counterparty's number with the counterparty's data.
7. **Reconciliation rules.** Tolerances and cadence for cross-checks (owner vs OEM vs offtaker availability; meter vs PPC energy); clock discipline per contract.

### Part 3 — KPIs (the targets)

8. **Promote sparingly.** Pick the short list that answers "are we performing" — typically the contractual guarantees vs their thresholds plus operating-limit compliance. Everything else stays a metric for reporting. `kpi_code` stays blank until the taxonomy ratifies the ID scheme.

## Facilitation guidance

- Keep the tree and the definitions consistent within the document: every purple KPI node must resolve to a metric definition (a full sheet or a compact-table row) below — a purple node with no definition is a gap to close in-session.
- When the contractual definition and operational habit differ, record both and mark the contractual one authoritative for money.
- Timezone/interval-convention errors (interval-beginning vs -ending, DST, per-contract evaluation clocks) are the most common silent corruption — ask explicitly.
- Tie each metric to implementation: where is the official number computed (platform model, dashboard, report script)? Uncomputable metrics go to the automation backlog.

## Outputs

1. `Metrics_Tree(MT)/metrics-tree.md` populated in place (tree + metric definitions + KPI table; version bumped).
2. Implementation backlog: metrics lacking a source or computation home; folder `todo.md` updated, session logged in the folder `log.md`.
3. Next steps: metrics feed the Monthly Performance Report (data product) and BESS GADS directly.
