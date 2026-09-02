---
name: data-interface-register
title: Data Interface Register — Facilitated Session
type: Skill
description: Build or update a BESS project's Data Interface Register from its Entity Interaction Map through a facilitated session with the asset manager or owner. Use whenever the user wants to catalogue data systems, telemetry chains, protocols, signal lists, integration points, or scope data-platform ingestion for a BESS project, including phrases like "map the data flows", "what systems do we need to connect", "telemetry inventory", or when scoping a historian/data-platform/APM integration. Requires a project EIM (if none exists, run the eim-review-build skill first).
---

# Data Interface Register: Facilitated Session

> **Before you start:** read `Definitions(DEF)/definitions.md` (what every acronym and term means on this project; ⚠️-flagged entries carry two meanings) and `Ontology(ONT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Template: `Data_Interface_Register(DIR)/data-interface-register.md` (repo root). The register is the foundational satellite document: KPIs, settlement reconciliation, and the monthly report all reference its interface IDs.

## Prerequisites

- Project EIM (version-pinned; record `EIM_VERSION` in the header). If missing or stale, offer the `eim-review-build` skill first.
- Useful inputs to request up front: points lists / tag databases, network diagrams, EMS API docs, ISO telemetry specs.

## Session flow

Work the template's sections in order; populate from the EIM automatically before asking anything.

### Step 1: Pre-fill from the EIM (no questions yet)
Extract every purple (`data`) node into the System Inventory and every edge touching a data node into the Interface Register, with EIM node/edge references filled in. Show the pre-filled skeleton so the session starts from 60%, not 0%.

### Step 2: System Inventory pass (2–4 questions per batch)
For each system: vendor/product, who owns and administers it, where it runs, how it's accessed. Prioritize systems on the critical chains:
1. BMS → battery controller → PPC → RTAC → ISO (market-facing)
2. EMS → APM (the owner's data tap)
3. POI meters → settlement

### Step 3: Interface pass
For each interface on the critical chains: protocol, rate, format, network path, criticality. Don't grind through low-value edges live; mark them `TBD` and assign homework.

### Step 4: Hand off the telemetry detail (do not build point lists here)
The register stays at the system/interface grain. Point- and tag-level detail belongs to its own homes and the session only routes it: grid/market-facing points (APD/APC/SOC/MAXENER/Mode or equivalents, per-point definitions and validation) go to the **Grid Telemetry Map** data product (`data-product` scope if it doesn't exist yet); vendor tag semantics to the OEM telemetry extraction in `Project_Documentation/`; per-guarantee capture requirements (what to historize, resolution, retention) to the **PGM §6 Shadow Calculation Requirements**. If the session surfaces point-level facts, park them for the right home rather than growing tables here.

## Facilitation guidance

- The asset manager often won't know protocols and rates; that's normal. Capture the *person who would know* (LTSA controls engineer, EMS OEM) as the homework owner.
- Watch for chain-of-custody transformations: SOC reported by the EMS is rarely the same number the Master BMS computes. Whenever the user says "we get SOC from X," ask what X derives it from.
- Criticality = what breaks if the interface fails: market participation (H), contractual evidence (H), convenience (M/L).

## Outputs

1. `Data_Interface_Register(DIR)/data-interface-register.md` populated in place (version bumped in the header).
2. Open-items table filled with owners and dates (this is the follow-up agenda).
3. Offer next steps: the Grid Telemetry Map session for point-level detail; the `metrics-tree` skill for the metrics the captured data feeds; PGM §6 for platform/ingestion scoping.
