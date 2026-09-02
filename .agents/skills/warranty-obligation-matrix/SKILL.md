---
name: warranty-obligation-matrix
title: Warranty Obligation Matrix — Facilitated Session
type: Skill
description: Build or update a BESS project's warranty obligation matrix from its Entity Interaction Map through a facilitated session with the asset manager or owner. Use whenever the user wants to inventory defect-style warranties (battery/PCS/EMS defect, EPC workmanship, BOP equipment, serial-defect provisions), map claim paths, prepare or evaluate a defect warranty claim, or track owner-side conditions that keep coverage valid (cycle limits, temperature envelopes, PM compliance, firmware). Measured-performance guarantees (availability, capacity, RTE, dispatch) belong to the performance-guarantee-matrix skill instead. Requires a project EIM (run eim-review-build first if none exists).
---

# Warranty Obligation Matrix: Facilitated Session

> **Before you start:** read `Definitions(DEF)/definitions.md` (what every acronym and term means on this project; ⚠️-flagged entries carry two meanings) and `Ontology(ONT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Template: `Warranty_Obligation_Matrix(WOM)/warranty-obligation-matrix.md` (repo root). Scope: **defect-style warranties** ("it broke / it was built wrong"). Measured performance levels tied to LDs live in the Performance Guarantee Matrix (`performance-guarantee-matrix` skill); if the user brings those up, note them and hand off.

## Prerequisites

- Project EIM (the OEM/EPC/LTSA branch enumerates the instruments).
- Strongly request before or during the session: the actual warranty schedules and EPC warranty articles. Without clause references the matrix is a sketch, not a tool.

## Session flow

### Step 1: Pre-fill warranty inventory from the EIM
Every warranty node on the map becomes a row (battery defect, PCS, EMS/controller, BESS EPC workmanship, substation EPC workmanship, BOP equipment warranties, serial-defect provisions, insurance as risk backstop). Confirm which exist on this project, add missing ones, delete inapplicable ones.

### Step 2: Per instrument: the five questions
For each row: (1) what is covered (defect class), (2) over what term and from what trigger (substantial completion? delivery? COD?), (3) what's the remedy (repair / replace / re-perform) and any caps, (4) what's the claim path (verify against the EIM; e.g., claims routed via the LTSA provider), (5) clause reference. If the user can't cite the clause, mark it and assign retrieval homework.

### Step 3: Owner-side obligations (the compliance lens)
What must the owner/operator do to keep warranties valid? Cycle/throughput limits, SOC windows, temperature envelopes, PM per OEM schedule, approved firmware, storage conditions. For each: how is compliance monitored today, and from which data? Gaps here are urgent: breaching a warranty condition silently is the most expensive data-gap there is. (These same conditions often also gate the performance guarantees; cross-reference the Performance Guarantee Matrix.)

### Step 4: Calendar and claims
Populate key dates (expiries, defect notification deadlines, inspection windows) and migrate any live claims into the claims log with their evidence packs.

## Facilitation guidance

- Distinguish *warranty* (defect) from *guarantee* (performance level): users blur them, contracts don't. This session covers the former only.
- When the EIM shows warranty management via the LTSA, confirm whether the owner retains direct privity for claims; it changes the claim path.
- Serial-defect provisions deserve their own row: threshold, fleet scope, and remedy differ from unit defect terms.

## Outputs

1. `Warranty_Obligation_Matrix(WOM)/warranty-obligation-matrix.md` populated in place (version bumped in the change log).
2. Owner-side compliance gaps → candidate monitoring/alerting work for the data platform.
3. Any performance-guarantee items surfaced → handoff list for the `performance-guarantee-matrix` skill.
