---
type: Guide
title: Offtake / Tolling Agreement Review Instructions
description: How to review an offtake or tolling agreement step by step - which companion files to produce, what goes in each, and the verification passes that close the review.
---

# Offtake / Tolling Review Instructions

How to review the documents in this folder — the offtake or tolling agreement, its exhibits, and amendments — from a performance-engineering perspective. The mechanical workflow lives in the [`project-document-review`](/.agents/skills/project-document-review/SKILL.md) skill; this file is the domain-specific method.

**The lens is inverted from the service agreement.** Under the service agreement the owner *holds* guarantees; under the offtake the owner *gives* them. Every guarantee here is an exposure, every excused event is a shield, every reporting duty is work someone must be staffed to do — and the counterparty's numbers arrive with money already deducted. **Read it as the defendant, not the claimant.** (Its mirror is [`LTSA/instructions.md`](../LTSA/instructions.md).)

**Output = companion files**, each referenced from `summary.md` and the folder `index.md`:

| File | Holds |
|---|---|
| `definitions.md` | Every defined term, one line each, source clause, usage map — with searchable acronym keys |
| `guarantees.md` | Everything the owner owes: availability calculation, capacity, efficiency, dispatch compliance, **every payment deduction**, excused events, usage limits, the test regime |
| `outage-management.md` | Who manages outages, notice regimes and deadlines, excused vs charged, attribution control, the system of record |
| `telemetry.md` | Every data point the agreement requires: definition, measured vs calculated, interface, consumer |
| `summary.md` | Key facts + reporting, communication protocols, personnel, payment flow — pointers to the files above |
| `red-flags.md` | Features most likely to hurt the owner: clause as written (cited), why it bites, mitigation |

Keep everything **short, cited, and human-readable**.

## Step 1 — Definitions and counterparties → `definitions.md`

Exact legal entities and roles — the offtaker may wear several hats (offtaker, transmission owner, scheduling coordinator); keep the roles separate. Extract **all defined capitalized terms**, one line each. Close with the **mechanical completeness check** (grep `"X" means` across all operative exhibits, diff against the file, record the audit scope) and give load-bearing terms **searchable acronym keys**. ⚠️ every term the supplier-side contracts define differently — availability, efficiency, capacity, planned outage.

## Step 2 — What the owner owes → `guarantees.md`

Mandatory sections:

1. **Availability guarantee and its calculation** — the exact formula, denominator, clock (contract year vs calendar), outage-hour weighting, exclusions, and who computes it. Compare line by line against the supplier-side availability metric: **the deltas are uncovered exposure.**
2. **Capacity guarantee** — test-measured capacity, adjustment tiers, deemed-capacity rules (including any capacity-deemed-zero trap), augmentation rights.
3. **Efficiency** — how measured (test vs operational average), how applied to payments, temperature treatment or its absence, the guaranteed table in full.
4. **Dispatch compliance** — the band, the measurement, the adjustment.
5. **Payment deductions and adjustments — enumerate every single one.** For each: trigger, formula, price basis, cap (or "uncapped"), clock, and which KPI predicts it. The monthly invoice is the sum of these; **the review isn't done until the full deduction list reconciles against the payment section.**
6. **Excused events** — force majeure, planned-outage windows (caps, notice), grid and curtailment treatment, and notice-conditioned excuses. Mark each: also excused under the supplier contracts?
7. **Usage limits** — cycles per day and year, throughput caps, SOC windows: the offtaker's rights that drive supplier-side warranty erosion.
8. **Test regime** — who calls tests, frequency, seasonal restrictions, notice, acceptance and rejection clocks, retest rights, deemed results.

## Step 3 — Outage management → `outage-management.md`

The operational heart of an offtake agreement:

- **Who declares and manages outages** — owner, operator, or offtaker; the request/approval workflow and scheduling authority.
- **Notice regimes with deadlines** — planned-outage notice periods, forced-outage notification clocks, and any **notice-conditioned excuse** (a deadline that preserves an exclusion — miss it and the outage is charged).
- **Excused vs charged, as an explicit table** — for each cause (equipment, balance of plant, comms, grid, force majeure, offtaker-caused): excluded from the denominator, charged, or conditionally excused?
- **Attribution control** — who classifies an outage, what evidence the counterparty can demand, how the owner contests a classification, and the dispute clock. If the owner reports and counterparty silence is deemed acceptance, say so: it reverses the burden.
- **The system of record** — where outages live (owner's log, counterparty's system, or both) and the reconciliation duty across ledgers.
- Deferability rules, restoration duties, and re-test triggers after outages.

## Step 4 — Telemetry and interfaces → `telemetry.md`

- **Every telemetry point the agreement names or requires**: definition as written, units, update rate, and — critically — **measured or calculated?** For each calculated point (availability %, state of charge, deliverable capacity), pin down the formula, the inputs, and who computes it. **A calculated point with no contractual formula is the owner's to define — and a finding.**
- The **interfaces**: dispatch path, notice channels, meter data, settlement data, report delivery. Map each to the Data Interface Register.
- Data-reporting duties (native-format delivery, retention, audit rights) and what the counterparty can demand on request.
- The tags the historian needs to *defend* every number in Steps 2–3.

## Step 5 — Summary → `summary.md`

Key facts (parties, term, offtake structure, all payment components), then **reporting obligations** (who calculates what, who reports, deadline, acceptance mechanics), **communication protocols** (notices, dispatch channel, dispute path, deemed-acceptance clocks), **personnel**, **payment & settlement flow** (invoice cycle, deduction mechanics, dispute window), and pointers to all companions.

## Step 6 — Keyword scan (verification pass)

Grep for obligation (*shall, must, responsible*), communication (*notify, notice, report, submit, consent, deemed*), money (*pay, adjustment, deduction, liquidated damages, reduce, cap*) and time (*within, days, hours*) keywords; confirm every hit is captured or consciously excluded. Record scope in `log.md`.

## Step 7 — Definitions cross-reference (verification pass)

Look up every use of each load-bearing term; confirm the operating clauses are captured; add "used in" notes where relevant.

## Step 7b — Red flags → `red-flags.md`

Anything structurally against the owner: one-sided deemed rules, uncapped deductions, notice-conditioned excuses, metrics that don't match the supplier-side twin, numbers the counterparty calculates. As written / why it bites / mitigation.

## Step 8 — Close out

Indexes, folder `log.md`, `todo.md`, glossary roll-up **in the same session**, and propagate: guarantee-matrix rows and calc sheets, metrics tree, interface register, the GADS excused/charged mapping, escalation-plan notice rows, `project_info.md`.

## Known traps (offtake-specific)

- **Deemed acceptance running against the owner** — the owner reports and the counterparty gets review windows; but *their* deductions may need active dispute within a clock. Find both clocks.
- **Notice-conditioned excuses** — an exclusion that exists only if a notice went out on time converts an operational lapse into availability damage.
- **One test prices a year** — a test-measured efficiency or capacity applied monthly makes test day the highest-leverage day of the year; check seasonal windows and retest rights.
- **"Availability" ≠ the supplier's availability** — different formula, boundary, clock. Never compare headline numbers across contracts.
- **The deduction stack compounds** — availability LD + capacity adjustment + efficiency adjustment + dispatch adjustment can all hit the same month. Check for anti-double-counting language; its absence is a finding.
- **Usage rights vs supplier limits** — the offtaker's cycling rights may exceed what the supplier guarantee tolerates. Quantify the gap.
- **Uncapped exposure** — offtake LDs are commonly uncapped while supplier recovery is capped. State the net.
