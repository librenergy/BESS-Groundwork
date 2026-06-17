---
name: warranty-obligation-matrix
title: Warranty & Performance Guarantee Obligation Matrix — Facilitated Session
type: Skill
description: Build or update a BESS project's performance guarantee and warranty obligation matrix from its Entity Interaction Map through a facilitated session with the asset manager or owner. Use whenever the user wants to inventory warranties (battery capacity/defect, PCS, EMS, EPC workmanship), availability guarantees, LTSA LD structures, tolling performance obligations, map guaranteed metrics to data sources, prepare or evaluate a warranty claim, or track owner-side warranty compliance conditions (cycle limits, temperature envelopes). Requires a project EIM (run eim-review-build first if none exists).
---

# Warranty & Performance Guarantee Obligation Matrix — Facilitated Session

Template: `Warranty_Obligation_Matrix/03-warranty-obligation-matrix.md` (repo root). This is where Performance Engineering earns its keep: every guaranteed metric must map to data the platform can produce, because whoever holds the data wins the dispute.

## Prerequisites

- Project EIM (the OEM/EPC/LTSA branch enumerates the instruments).
- Strongly request before or during the session: the actual warranty schedules, LTSA availability annex, tolling agreement performance exhibit. Without clause references the matrix is a sketch, not a tool.

## Session flow

### Step 1 — Pre-fill instrument inventory from the EIM
Every warranty/SA/guarantee node on the map becomes a row (battery defect + capacity warranties, OEM availability guaranty, PCS warranty + SA, EMS warranty + SA, LTSA availability, both EPC workmanship warranties, tolling obligations). Confirm which exist on this project, add missing ones (performance bonds? augmentation agreements?), delete inapplicable ones.

### Step 2 — Per instrument: the five questions
For each row: (1) what metric is guaranteed, (2) over what term, (3) what's the remedy and cap, (4) what's the claim path (verify against the EIM — e.g., claims routed via LTSA), (5) clause reference. If the user can't cite the clause, mark it and assign retrieval homework.

### Step 3 — Metric → measurement → data mapping (the core)
For each guaranteed metric, pin down: contractual definition, formula, measurement boundary (POI vs. PCS terminals vs. DC — boundaries decide disputes), the authoritative data source, and its Data Interface Register reference. Where the contract is silent on methodology, flag it loudly — silent methodology is a future dispute.

### Step 4 — Owner-side obligations
Flip the lens: what must the owner/operator do to keep warranties valid? Cycle/throughput limits, SOC windows, temperature envelopes, PM compliance, approved firmware. For each: how is compliance monitored today, and from which data? Gaps here are urgent — breaching a warranty condition silently is the most expensive data-gap there is.

### Step 5 — Calendar and claims
Populate key dates (expiries, capacity test windows, notice deadlines) and migrate any live claims into the claims log.

## Facilitation guidance

- Distinguish *warranty* (defect) from *guarantee* (performance level) — users blur them, contracts don't.
- Capacity warranty curves: get the actual curve (table or formula) and the test procedure reference; "85% at year 10" without the measurement procedure is unenforceable.
- When the EIM shows warranty management via the LTSA, confirm whether the owner retains direct privity for capacity claims — often yes, and it changes the claim path.

## Outputs

1. `03-obligation-matrix-{{project}}-v{{n}}.md` — populated template.
2. List of metrics lacking a defined data source → feeds the Data Interface Register and KPI Definitions skills.
3. Owner-side compliance gaps → candidate monitoring/alerting work for the data platform.
