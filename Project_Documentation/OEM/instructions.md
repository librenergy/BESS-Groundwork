---
type: Guide
title: OEM Documentation Review Instructions
description: How to review OEM technical documentation - data sheets, system specifications, installation and O&M manuals - into ratings, envelope, telemetry, and response-time extractions.
---

# OEM Documentation Review Instructions

How to review the documents in this folder — **OEM technical documentation**: data sheets, system specifications, installation and design manuals, application notes. One subfolder per vendor. **Not contracts** — the supply agreement, service agreement, and warranty live in their own domain folders; this folder holds the *engineering descriptions of the equipment*, which the contracts incorporate by reference.

**The lens: these documents define what the equipment can physically do and what the owner can see.** Contracts guarantee numbers; the OEM documentation determines whether the plant can meet them and whether the owner's telemetry can verify them.

Three rules specific to this domain:

1. **Version control is the first check.** Specs are living documents the OEM revises; the contract exhibits pin a governing revision. Record this copy's revision and date, find which revision the contracts incorporate, and state which governs. A standalone spec newer or older than the contractual one is a finding, not a nuisance.
2. **No definitions pass.** These are not contracts — skip the defined-terms extraction; capture the glossary-worthy technical terms (telemetry flags, mode names) directly into the master glossary.
3. **The OEM's own "not guaranteed as written" disclaimers matter.** Specs routinely state that telemetry/controls may not be implemented exactly as written. Quote the disclaimer; it decides how hard a spec number can be leaned on in a dispute.

**Output = extractions**, referenced from the vendor subfolder's `index.md`:

| File | Holds |
|---|---|
| `summary.md` | Document identity + revision, product architecture, ratings and envelope, the disclaimers |
| `telemetry.md` | Signals and alerts by interface and level, update rates, **granularity vs the warranty thresholds**, configurable parameters, response times |

## Step 1 — Identity and architecture

Revision, publication date, governing-revision check (rule 1). Product architecture: what is the unit, what are its field-replaceable components (module counts per unit — **these are the denominators warranty thresholds count in**), integrated vs separate PCS, thermal system, controller.

## Step 2 — Ratings and operating envelope → `summary.md`

Unit and configuration ratings (power/energy per option code), voltage/frequency windows and ride-through, temperature envelope and derate curves, aux consumption, cycling assumptions. Cross-check each against the contractual envelope (the supply agreement's test conditions, the service agreement's operating conditions, the offtake's usage rights) — a spec envelope narrower than a contractual right is a finding.

## Step 3 — Telemetry → `telemetry.md`

Every interface (protocol, level: site vs unit), every signal table with the signal's *semantics* (what "available" subtracts, what "nominal" includes). Then the question that matters: **can the owner's telemetry observe the thresholds the warranties count in?** If a warranty counts modules and telemetry stops at units, say so and derive the workaround (e.g. capability-quantum inference). Note which signals are hardware-capable but not implemented, and the data-rights boundary (what the OEM withholds).

## Step 4 — Response times and dynamics

Transition times between states, rise/settling/steady-state times with their **units recorded exactly (ms vs s)**, overshoot, ramp-rate configurability and defaults, droop/gain adjustability. Cross-check against every contractual response obligation (offtake dispatch windows, interconnection ramp limits) — and against the *test* configuration (tests often run unramped).

## Step 5 — Close out

Vendor subfolder `index.md`; folder `log.md`; glossary entries for telemetry flags and mode names; propagate: Data Interface Register (signals/interfaces), Metrics Tree (measurement semantics), the warranty matrices (observability of thresholds), EIM (architecture corrections).
