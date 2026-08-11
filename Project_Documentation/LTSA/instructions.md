---
type: Guide
title: Service Agreement Review Instructions
description: How to review an OEM long-term services agreement step by step - which companion files to produce, what goes in each, and the verification passes that close the review.
---

# Service Agreement Review Instructions

How to review the documents in this folder — the OEM/integrator long-term services agreement, its exhibits, and amendments. The mechanical workflow (file placement, text extraction, page-image checks, indexes) is the [`project-document-review`](/.agents/skills/project-document-review/SKILL.md) skill; this file is the domain-specific method.

**The lens: the owner *holds* these guarantees.** Read it as the claimant — what is promised, what excuses the promise, and what the owner must do to keep cover alive. (Its mirror is [`PPA/instructions.md`](../PPA/instructions.md), where the owner *gives* the guarantees.)

**Output = companion files**, each referenced from `summary.md` and the folder `index.md`:

| File | Holds |
|---|---|
| `definitions.md` | Every defined term, one line each, source clause, usage map for load-bearing terms |
| `guarantees.md` | Everything the provider guarantees or must do: capacity, availability, efficiency, excused events, operating conditions, LDs |
| `operations-and-maintenance.md` | How the provider maintains the equipment while the owner operates it |
| `telemetry.md` | Telemetry points, data access, data ownership and sharing |
| `summary.md` | Key facts + reporting, personnel, communication protocols, costs — with pointers to the files above |
| `red-flags.md` | Features most likely to hurt the owner: clause as written (cited), why it bites, mitigation |

Keep everything **short, human-readable, and cited** (exhibit/section for every claim). These files get read again — write them so that's true.

## Step 1 — Definitions and counterparties → `definitions.md`

Start here, before reading anything else.

- Identify the counterparties: exact legal entities, notice parties (cc's reveal sponsors and lenders), representatives, and every third party the agreement references.
- Extract **all defined capitalized terms**, one line per term. They span exhibits and sibling contracts and carry the real meaning — a guaranteed level is usually a defined term referenced everywhere else.
- Flag terms **defined in a document not held** (commonly the equipment supply agreement or the equipment warranty) — each is an open item, never a guess.
- ⚠️ terms that collide with another project contract ("availability", "efficiency", "capacity" mean different things per contract).
- **Close with a mechanical completeness check**: grep the full text for quoted definitions (`"X" means`, `"X" has the meaning`) across *all* operative exhibits and diff against the file. Coverage is checkable, so check it — and record the audit scope in the header, including which exhibits were excluded as boilerplate.
- Give load-bearing terms **searchable acronym keys** in the thematic tables, not just spelled-out names — people search by acronym.

## Step 2 — Provider obligations → `guarantees.md`

Everything the provider **must do or is guaranteeing**. Mandatory sections:

1. **Capacity / energy retention** — the full year-by-year table and the test that measures it.
2. **Availability** — the formula *with its denominator*, planned-outage allowances, and who computes it from whose data.
3. **Efficiency** — table, test, temperature adjustments.
4. **Excused events** — the complete list. Mark each: is it also excused under the offtake agreement? **The asymmetries are the owner's exposure.**
5. **Operating conditions** — everything the owner must hold for the guarantees to stand: loss allowances, voltage windows, temperature envelope, access and isolation turnaround, remote access, software updates, throughput and cycling limits. These quietly void coverage; write them as monitored limits.
6. **LDs and caps** — rates (per-day rates compound), caps converted into physical shortfall, accrual start/stop, sole-remedy language.
7. **Warranty obligations** — services warranty, response times, the split against the equipment warranty, claim path.
8. **Testing regime** — who calls, notice, cost of a passing extra test, retest deadlines, preconditions and derates.
9. **Guarantee erosion and termination** — throughput ceilings modelled against the offtake agreement's cycling rights; state the year cover ends.

## Step 3 — O&M → `operations-and-maintenance.md`

Preventative maintenance scope and frequency; corrective and warranty work (included vs billed); remote monitoring and **response times**; coordination with the owner (site access, isolation, blackout windows, onsite infrastructure the owner must provide); personnel, subcontracting and certified-servicer programs; records — what the provider keeps, what the owner can get, and when.

## Step 4 — Telemetry and data → `telemetry.md`

The points the guarantees are computed from, recording rates, meter and time-sync requirements, **data ownership and access** (what is the owner's, what the provider withholds, what arrives on termination), and the historian tags needed for shadow calculations.

## Step 5 — Summary → `summary.md`

Key facts up front (parties, term, **all** fees — including any extended-warranty or buried ones — measurement basis, guarantee overview), then: **reporting obligations** (the provider's own working papers on its guarantee — calendar them), **personnel & organization**, **communication protocols** (claims, performance concerns, test requests, emergency notification, invoice disputes), **costs & settlement**, and pointers to the other companions.

## Step 6 — Keyword scan (verification pass)

Grep the full text and confirm every hit is captured or consciously excluded:

- **Obligations:** shall, must, will provide, obligated, responsible for, at its cost
- **Communication:** report, notify, notice, submit, request, deliver, consent, approval
- **Money:** pay, fee, liquidated damages, invoice, reimburse, credit, cap, refund
- **Time:** within, days, hours, deadline, promptly, immediately

Record the scan (date + keyword sets) in the folder `log.md`.

## Step 7 — Definitions cross-reference (verification pass)

For each term in `definitions.md`, look up its uses. Confirm every clause that *operates* on the term is captured. Add a one-line "used in" note to load-bearing entries; skip boilerplate.

## Step 7b — Red flags → `red-flags.md`

Anything structurally against the owner — a metric that measures less than its name claims, an asymmetric adjustment, a condition ordinary operation will violate, a coverage gap between contracts — as a numbered flag: **as written** (cited, page-image-verified), **why it bites**, **mitigations**. This is the file non-specialists read; keep each flag self-contained.

## Step 8 — Close out

Update both indexes, write the folder `log.md` entry, park open items in `todo.md`, roll the definitions up into the master glossary **in the same session**, and propagate: guarantee-matrix rows and calc sheets, metrics-tree authoritative sources, interface-register tags, `project_info.md`.

## Known traps

- **Variables defined elsewhere.** Guarantee formulas referencing terms defined in an unheld supply agreement — record as written, never substitute nameplate values.
- **Self-assessment.** The provider computes its own availability from its own controller. Without a shadow calculation the owner argues from the counterparty's number.
- **`max(charge, discharge)` availability** scores a one-sided derate as fully available while the offtaker counts it unavailable.
- **Aggregate basis** absorbs dead units invisibly.
- **Per-day LD rates** compound until a passing retest — the retest clause is an economic term.
- **Non-monotonic table values** in an executed document: verify against the page image, transcribe as executed, footnote — never silently correct.
- **Fees outside the fee table.** Extended warranties, test charges, change-order exposure — the keyword scan is what catches them.
