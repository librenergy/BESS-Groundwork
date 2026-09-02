---
name: ontology
title: Groundwork Ontology — Facilitated Session
type: Skill
description: Build or update the project's Groundwork Ontology in Ontology(ONT)/ — the segment taxonomy, the measurement vocabulary, and the canonical data models that every other document's terms resolve to. Use whenever the user wants to define or standardize segment types (BESS, PCS, battery string, DC bus, transformer), ratify measurement names and units (cell voltage, state of charge, available power), build or extend a canonical data model for a segment type, resolve "same quantity, two names/units" confusion between sources, or generate the spec an agent uses to build dimension data and canonical tables on a data platform. Also use when someone asks "what is a <segment>?" or "what exactly is <measurement>?" and the answer should become a ratified term. Best run after Definitions & Taxonomy and before the Data Interface Register and Metrics Tree, so those documents are written in ratified terms.
---

# Groundwork Ontology — Facilitated Session

> **Before you start:** read `Definitions(DEF)/definitions.md` and `taxonomy.md` if populated; ONT is the identifier authority and DT the prose authority, and each ratified term gets a glossary roll-up in the same session. Read `Ontology(ONT)/ontology.md` for the layer model and rules.

Populates **`Ontology(ONT)/`** in place: `segment-types.md` (layer 1), `measurements.md` (layer 2), `canonical-models.md` (layer 3), and the project decisions in `platform-projection.md`. Layer 4 (metrics) is owned by the Metrics Tree; this session only keeps the `metric_code` registry in step.

## Prerequisites

- None hard. Ideally the project document reviews and the DT glossary exist (contract vocabulary informs term ratification). If tag lists or an existing platform schema are available, load them: ratification against real tags beats blank-page invention.

## Session flow

### Part 1 — Segment taxonomy (prune, don't invent)

1. **Walk the seeded taxonomy against the plant actually built.** Delete absent types, re-hang containment parents to match the architecture (AC-block vs DC-block, per DT taxonomy §2), and capture the vendors' words for each type as synonyms.
2. **New types need all columns**: ID, parent, one-line definition, synonyms, plus a glossary entry. Challenge each addition: is it a type, or an instance of an existing type?

### Part 2 — Measurement vocabulary (the ambiguity hunt)

3. **Ratify term by term, in small batches.** For each: exactly one unit, one basis, one sign convention, and the "is not" line. The productive question is always the near-miss: percent vs energy SoC, capability vs delivered power, setpoint vs response, cell vs ambient temperature.
4. **Hunt existing pollution** if a platform registry exists: grains masquerading as types, unit variants (`Mwh`/`MWh`), catch-all generics, ratio-vs-percent ambiguity. Each find becomes a ratified term plus an ingestion-mapping note, never a second term.
5. **Apply the kind test** to every disputed quantity: arrives from the plant → measurement; recomputable from stored data → metric (send it to the Metrics Tree); static → parameter.

### Part 3 — Canonical models (one per sitting)

6. **Pick the segment type whose telemetry matters next** and ratify its model column by column against real tag lists: term, statistic, unit, required-or-optional. Required means "the model is not implemented for a site until this is populated", so mark it deliberately.
7. **Keep derived quantities out.** A canonical table carries measurements; roll-ups computable from a lower model are metrics or downstream views.

### Part 4 — Projection and close-out

8. **Fill or update the decision table** in `platform-projection.md` (platforms, replication, interval convention, naming, currency). If the user wants the platform built now, hand the projection's agent directives to a data-platform agent; otherwise record it as the standing handoff.
9. Close per house convention: version bumped in `ontology.md`, `ONT_VERSION` noted for consuming documents, glossary rolled up, folder `log.md` entry, open `❓` items to `todo.md`. Suggested next skills: `data-interface-register` (bind terms to systems), `metrics-tree` (write formulas in ratified terms).

## The fill loop (steady state)

After the one-time taxonomy prune, the ontology fills **on demand, pulled by KPI-building work** (the "fill loop" section of `ontology.md`). When the user is building a metric: classify it in the Metrics Tree (contractual per contract / physical OBE / standard), write its formula in measurement terms, ratify any missing term at that moment, add missing canonical-model rows at the grain the telemetry actually reports, then build the calc against canonical columns. Never run ahead of the work by ratifying speculatively.

## Facilitation guidance

- **Never rename casually.** Term IDs and column names are stable identifiers; a rename is a breaking change with a sweep, and the session should say so out loud before agreeing to one.
- Unknowns get `❓` + owner + date and the session moves on; a `❓` model is skipped by build agents, which is the designed safe behavior.
- When two sources report the "same" quantity differently, the ontology holds one term and the difference moves to the binding layer (DIR) or the Metrics Tree's source-of-truth table. Do not mint `soc_vendor2`.
- Contractual measurement definitions (boundary, basis) outrank vendor tag semantics; cite the clause or flag homework.

## Outputs

1. `Ontology(ONT)/` layers updated in place, version bumped.
2. The platform projection ready for (or executed by) a data-platform build agent.
3. Glossary roll-up, folder `log.md` entry, `todo.md` updated.
