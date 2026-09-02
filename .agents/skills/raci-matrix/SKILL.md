---
name: raci-matrix
title: RACI Matrix & Escalation — Facilitated Session
type: Skill
description: Build or update a BESS project RACI matrix (Responsible/Accountable/Consulted/Informed) for operational events and processes, including its escalation & communication sections (severity levels, who-calls-whom chains, notification deadlines, standing meeting cadences, contractual notification obligations), derived from the project's Entity Interaction Map, through a facilitated session with the asset manager. Use whenever the user wants to clarify roles and responsibilities, define who handles plant trips/outages/warranty claims/telemetry faults, resolve "who owns this" confusion between LTSA/GOP/SC/OEMs, onboard a new operations team, define who-calls-whom for plant events, or capture contractual notification deadlines (insurer, offtaker, lender, force majeure). Requires a project EIM (run eim-review-build first if none exists); the escalation sections also need the Contact Register.
---

# RACI Matrix & Escalation: Facilitated Session

> **Before you start:** read `Definitions(DEF)/definitions.md` (what every acronym and term means on this project; ⚠️-flagged entries carry two meanings) and `Ontology(ONT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Template: `RACI_Matrix(RACI)/raci-matrix.md` (repo root). One document, two parts: the RACI (sections 1–5) translates the EIM's contractual edges into operational accountability, scenario by scenario; the escalation & communication sections (6–10) turn those accountable parties into a 2 AM call sheet with deadlines. The parts can be built in one sitting or two: the RACI first, always.

## Prerequisites

- Project EIM (contractual `===` edges are the starting hypothesis for accountability).
- For the escalation sections: Contact Register (names + 24/7 numbers).
- Helpful inputs: LTSA scope schedule, GOP agreement, SC agreement, optimizer agreement. The contracts settle arguments the diagram can't.

## Session flow, Part A: the RACI (sections 1–5)

### Step 1: Pre-fill entity legend and hypothesis RACI
From the EIM, populate the entity legend (drop entities not on this project, add ones missing). Pre-fill the template's default R/A/C/I assignments as a *hypothesis*; make clear these are defaults to challenge, not answers.

### Step 2: Walk by scenario, not by cell
Go category by category (real-time ops → outages → maintenance/warranty → market → reporting). For each row, narrate the scenario concretely: "It's 2 AM, the plant trips on undervoltage. Walk me through the first hour — who sees it, who acts, who must be told?" The narrative reveals the true R; the contract names the A.

### Step 3: Enforce the single-A rule
Exactly one Accountable per row. When the user names two (very common for GOP vs. LTSA, or AM vs. AO), stop and resolve: usually one is A and the other is R or C. If genuinely ambiguous, log it in the Discrepancies table; these are real contractual gaps worth flagging to the owner.

### Step 4: Cross-check against the EIM
Where the agreed RACI implies an interaction the EIM doesn't show (or contradicts one it does), flag it. Either the map or the RACI is wrong; decide which and queue an EIM edit if needed.

### Step 5: Project-specific rows
Ask: "What has actually gone wrong on this project in the last year?" Add a row for each real incident type (e.g., telemetry signal stuck, auto-balancing misbehaviour, meter anomaly). Real history produces the rows that matter most.

## Session flow, Part B: escalation & communication (sections 6–10)

### Step 6: Severity levels
Calibrate the four levels to this project: what counts as SEV-1 here, and what notification windows do contracts/ISO rules impose?

### Step 7: Walk the event types
For each escalation-chain row, narrate the scenario and confirm: who detects it (which system raises the alarm; tie to EIM data nodes), first call, backup, accountable, must-notify list, and the binding deadline with its source (ISO rule, insurance clause, tolling agreement). Each chain's Accountable must match the A of the corresponding RACI row above; a mismatch is a Discrepancies entry.

### Step 8: Standing communications
Confirm the recurring cadences from the EIM (optimizer daily call/weekly report, O&M calls, monthly report distribution) and add the project's real meeting rhythm.

### Step 9: Contractual notifications
Mine the Performance Guarantee Matrix, Warranty Obligation Matrix, and insurance policies for hard notification deadlines; these are the rows where lateness costs money.

### Step 10: Quick card
Fill the print-friendly card with live 24/7 numbers from the Contact Register.

## Facilitation guidance

- Asset managers tend to claim A for everything; LTSAs tend to claim R for nothing outside scope. Anchor on contract language when positions conflict.
- High-value ambiguities to probe deliberately: ISO telemetry faults (GOP vs. SC vs. LTSA), firmware/control-parameter changes (who approves?), outage categorization (who decides if it's excluded?), warranty evidence assembly (who builds the pack?).
- Keep the matrix to events that recur or carry money/risk. A 200-row RACI is a RACI nobody reads.
- Deadlines need sources. "Notify the insurer promptly" → find the actual clause wording and days.
- Detection matters as much as response: if no system detects the event (e.g., a stale MAXENER), the chain never starts; log detection gaps as data-platform alerting backlog.
- Test the plan verbally: pick one SEV-1 and one SEV-2 and have the user walk it end-to-end against the draft. Gaps surface immediately.

## Outputs

1. `RACI_Matrix(RACI)/raci-matrix.md` populated in place (version bumped), including the quick card if Part B ran.
2. Discrepancies table with resolution owners (often the most valuable deliverable; it lists the contractual gaps).
3. Detection-gap list → alerting backlog.
4. Suggest a periodic drill / annual review date for the escalation sections.
