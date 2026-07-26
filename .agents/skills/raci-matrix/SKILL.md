---
name: raci-matrix
title: RACI Matrix — Facilitated Session
type: Skill
description: Build or update a BESS project RACI matrix (Responsible/Accountable/Consulted/Informed) for operational events and processes, derived from the project's Entity Interaction Map, through a facilitated session with the asset manager. Use whenever the user wants to clarify roles and responsibilities, define who handles plant trips/outages/warranty claims/telemetry faults, resolve "who owns this" confusion between LTSA/GOP/SC/OEMs, or onboard a new operations team. Requires a project EIM (run eim-review-build first if none exists).
---

# RACI Matrix — Facilitated Session

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Template: `RACI_Matrix(RACI)/raci-matrix.md` (repo root). The RACI translates the EIM's contractual edges into operational accountability, scenario by scenario.

## Prerequisites

- Project EIM (contractual `===` edges are the starting hypothesis for accountability).
- Helpful inputs: LTSA scope schedule, GOP agreement, SC agreement, optimizer agreement. The contracts settle arguments the diagram can't.

## Session flow

### Step 1 — Pre-fill entity legend and hypothesis RACI
From the EIM, populate the entity legend (drop entities not on this project, add ones missing). Pre-fill the template's default R/A/C/I assignments as a *hypothesis* — make clear these are defaults to challenge, not answers.

### Step 2 — Walk by scenario, not by cell
Go category by category (real-time ops → outages → maintenance/warranty → market → reporting). For each row, narrate the scenario concretely: "It's 2 AM, the plant trips on undervoltage. Walk me through the first hour — who sees it, who acts, who must be told?" The narrative reveals the true R; the contract names the A.

### Step 3 — Enforce the single-A rule
Exactly one Accountable per row. When the user names two (very common for GOP vs. LTSA, or AM vs. AO), stop and resolve: usually one is A and the other is R or C. If genuinely ambiguous, log it in the Discrepancies table — these are real contractual gaps worth flagging to the owner.

### Step 4 — Cross-check against the EIM
Where the agreed RACI implies an interaction the EIM doesn't show (or contradicts one it does), flag it. Either the map or the RACI is wrong; decide which and queue an EIM edit if needed.

### Step 5 — Project-specific rows
Ask: "What has actually gone wrong on this project in the last year?" Add a row for each real incident type (e.g., telemetry signal stuck, auto-balancing misbehaviour, meter anomaly). Real history produces the rows that matter most.

## Facilitation guidance

- Asset managers tend to claim A for everything; LTSAs tend to claim R for nothing outside scope. Anchor on contract language when positions conflict.
- High-value ambiguities to probe deliberately: ISO telemetry faults (GOP vs. SC vs. LTSA), firmware/control-parameter changes (who approves?), outage categorization (who decides if it's excluded?), warranty evidence assembly (who builds the pack?).
- Keep the matrix to events that recur or carry money/risk. A 200-row RACI is a RACI nobody reads.

## Outputs

1. `RACI_Matrix(RACI)/raci-matrix.md` populated in place (version bumped).
2. Discrepancies table with resolution owners (often the most valuable deliverable — it lists the contractual gaps).
3. Offer next steps: the accountable parties per event feed directly into the Escalation & Communication Plan skill.
