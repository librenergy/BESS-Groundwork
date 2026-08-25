---
type: Guide
title: Service Agreement Review Instructions
description: How to review an OEM/integrator long-term services agreement step by step - extraction discipline, exhibit inventory, the seven questions per guarantee, companion files, and the verification passes that close the review.
---

# Service Agreement Review Instructions

How to review the documents in this folder — the OEM/integrator long-term services agreement, its exhibits, and amendments. The mechanical workflow (file placement, indexing, propagation) is the [`project-document-review`](/.agents/skills/project-document-review/SKILL.md) skill; this file is the domain-specific method.

**The lens: the owner *holds* these guarantees.** Read it as the claimant — what is promised, what excuses the promise, and what the owner must do to keep cover alive. (Its mirror is [`PPA/instructions.md`](../PPA/instructions.md), where the owner *gives* the guarantees.)

**Output = companion files**, each referenced from `summary.md` and the folder `index.md`:

| File | Holds |
|---|---|
| `definitions.md` | Every defined term, one line each, source clause, usage map for load-bearing terms |
| `guarantees.md` | Everything the provider guarantees or must do: capacity, availability, efficiency, excused events, operating conditions, LDs and caps |
| `operations-and-maintenance.md` | How the provider maintains the equipment while the owner operates it |
| `telemetry.md` | Telemetry points, data access, data ownership and sharing |
| `summary.md` | Key facts + reporting, personnel, communication protocols, costs — with pointers to the files above |
| `red-flags.md` | Features most likely to hurt the owner: clause as written (cited), why it bites, mitigation |

Keep everything **short, human-readable, and cited** (exhibit/section for every claim). These files get read again — write them so that's true.

## Step 0 — Extraction discipline

Extract the full text once and work from it — an LTSA is a grep target, not a cover-to-cover read:

```sh
pdftotext -layout "<file>.pdf" /tmp/ltsa.txt
```

**Use `-layout`.** Every substantive LTSA value lives in a table, and the default extraction jumbles table columns — it will hand you the right numbers attached to the wrong rows.

Two rules that override convenience:

- **Numbers are quotes.** Every figure in a companion traces to a clause. Transcribe year-by-year tables *in full* — never "see the table in the exhibit". The table *is* the deliverable.
- **Render the page for anything surprising.** A value that breaks a monotonic column, a malformed number, an OCR-suspect figure: confirm it visually (`pdftoppm -f <page> -l <page> -r 130 -png`) before recording it, and say in the companion that you did. Executed contracts contain typos — record them **as written and flag the ambiguity**, never silently correct. (Seen in practice: an LD-cap figure printed with a truncated final digit — the kind of defect that reads as a plausible number until you check the page image.)

## Step 1 — Exhibit inventory

The exhibit list is the map. Get it from the table of contents before anything else, then locate where each exhibit actually begins so later steps can jump straight in (`grep -nE "^\s*EXHIBIT [A-Z]"`). Record every exhibit and sub-exhibit in a table with its title, and note specifically:

- Which are **`[Reserved]`** — reserved slots are not missing content, but an exhibit referenced in the body and *absent* from the list is a finding.
- Which are **referenced but not attached** — delivered by data room, or replaced by a proprietary-placeholder page. Each becomes an open item.
- Which exhibits **carry the money**: system spec, covered services, division of responsibility, fees, performance guarantees, test protocols, warranty, spares, report formats.

## Step 2 — Definitions and counterparties → `definitions.md`

- Exact legal entities, notice parties (cc's reveal sponsors and lenders), representatives, and every third party the agreement references.
- Extract **all defined capitalized terms**, one line per term — they span exhibits and sibling contracts and carry the real meaning; a guaranteed level is usually a defined term referenced everywhere else.
- Flag terms **defined in a document not held** (commonly the equipment supply agreement or equipment warranty) — each is an open item, never a guess.
- ⚠️ terms that collide with another project contract ("availability", "efficiency", "capacity" mean different things per contract).
- **Close with a mechanical completeness check**: grep for quoted definitions (`"X" means`) across *all* operative exhibits and diff against the file; record the audit scope in the header. Give load-bearing terms **searchable acronym keys**.

## Step 3 — Warranties (distinct from guarantees)

They belong to a different matrix. For each warranty capture: **scope** (equipment vs services, and what is expressly outside); **period and what starts it** — LTSA warranties usually begin where the *supply contract's* warranty ends, so the start date lives in another document; **carve-outs by component** (transformers, switches and auxiliaries are routinely on different clocks); **re-warranty on repair** and its ceiling; **exclusions with their carve-backs** — exclusions commonly evaporate if the equipment stayed in spec or the cause traces to the contractor, and an exclusion quoted without its carve-back is a misquote; **remedies and response times** including step-in rights; **assignability** (lenders ask); **manufacturer pass-through**.

Pull out separately every **owner-side condition that keeps coverage valid** — manuals, consumables, cycle limits, data access. These are the ones breached in practice; they go to the Warranty Obligation Matrix as compliance obligations.

## Step 4 — Provider guarantees → `guarantees.md`

Work the guarantee exhibit one guarantee at a time. For **each**, answer the same seven questions:

1. **Level** — the number, per period, for the whole term. Full table.
2. **Formula** — verbatim, with every term defined.
3. **Measurement basis** — which meter, what sample rate, which data system is authoritative, what boundary.
4. **Evaluation period** — and how a partial period is handled.
5. **LD rate and trigger** — per what unit, per what time base, from when it accrues. Per-day rates compound; the retest clause is an economic term.
6. **Cure right** — any fix-and-retest window before liability attaches, and any de-minimis threshold.
7. **Interactions** — whether this guarantee can be claimed concurrently with another on the same event.

Then the guarantee-specific depth:

**Capacity / energy retention.** Power (MW) is typically flat — confirm whether it degrades. Energy (MWh) is where the work is: the degradation curve, its state-of-health basis, and the duty cycle it prices in. Check **BOL oversizing** (useable capacity above the guaranteed figure is the buffer that delays exposure — compute the headroom). Find the **reset clause** for a below-Year-1 commissioning test, the **interpolation rule** for a late test, and any **permanent adjustment mechanism** (temperature excursion, aux loss) that converts an operating event into a lasting guarantee reduction. And ask **what the percentage is a percentage *of***: if the anchor value is measured under a different contract, the LTSA fixes only the curve's *shape* — say so rather than quoting percentages as if absolute.

**Augmentation — search for it, and record the absence.** A declining energy guarantee and an augmentation obligation are *alternative* commercial structures, not companions. Grep for it (`augment|capacity maintenance|top.?up|replenish|retrofit`), read every hit, and classify: the word appearing is not an obligation existing — hits are often risk allocation (augmentation-by-others as an excused event), not duty. Corroborate against covered services, the fee table, and spares — an agreement that genuinely funds augmentation prices it somewhere. If absent, work out **what stands in for it** (BOL overbuild; a guarantee that declines with SOH) and then ask the question that matters: **who is obliged to keep the *offtake* capacity whole?** If the offtake holds a flat obligation and this guarantee declines, tabulate the gap year by year and name the candidate documents where the obligation might live. **A verified absence is a finding in its own right.**

**Availability.** Power- or energy-based, and against what reference (nameplate, contract capacity, or the most recent test — if a test can **rebase the denominator**, a persistent derate migrates from an availability miss to a capacity miss; trace which LD applies). The formula with every term; any **cap on excused hours**; the **partial-outage proration rule with its worked example** (transcribe the example — it disambiguates the formula); the **equipment boundary** (a defined "Core Equipment" typically excludes BOP, substation, transformers — what is outside the boundary matters as much as the formula); any **seasonal split** (note when the LD rate is higher in the season with the lower guarantee); who computes it from whose data.

**Efficiency.** The exact ratio — which meter, **inclusive or exclusive of auxiliary load** (this single choice moves RTE by whole points); dedicated test or derived from the capacity test; temperature adjustments; the LD formula (usually priced as extra charging energy, not a flat rate — transcribe it).

**Everything tested but not guaranteed.** Response time, signal-following, ramp rate and reactive capability are frequently demoted to on-request functional tests with no level and no LD. **Compare that list against the offtake agreement: any performance the offtake guarantees outward that this agreement does not guarantee inward is unbacked exposure — the highest-value output of the review.**

## Step 5 — Excused events, per guarantee

Every guarantee has an exclusion list and they are **not the same list** — extract each separately, then compare. For each exclusion capture the **carve-back**. Watch for: caps on excused categories (planned-outage-hour caps, blackout windows, ceilings relative to forced-outage hours — hours beyond a cap usually stop being excused entirely); **repeat tests** reclassified as planned time that consumes the cap; force majeure defined here vs by cross-reference. Then mark each event: **is it also excused under the offtake agreement?** The asymmetries are the owner's exposure.

## Step 6 — Liability, LD caps, and security

No extraction is actionable without it — an LD claim against a small cap is a different conversation from an uncapped one. Capture: the overall liability cap and its basis; the **per-period LD cap** and which LDs count toward it; every **carve-out** that escapes the cap; the consequential-damages waiver and its exceptions; and the **security** the owner can draw on (withholding, bond, parent guaranty).

## Step 7 — O&M → `operations-and-maintenance.md`

PM scope and frequency; corrective and warranty work (included vs billed); remote monitoring coverage hours, alert triggers and **response times**; coordination duties (site access, isolation, blackout windows, onsite infrastructure the owner must provide); personnel, subcontracting, certified-servicer programs; records — what the provider keeps, what the owner can get, when.

## Step 8 — Telemetry and data → `telemetry.md`

The points the guarantees are computed from, recording rates, meter and time-sync requirements, **data ownership and access** (what is the owner's, what the provider withholds, what arrives on termination), and the historian tags needed for shadow calculations.

## Step 9 — Summary → `summary.md`

Key facts up front (parties, term, **all** fees including buried ones, measurement basis, guarantee overview), then: **reporting obligations** — every provider report with deadline and minimum contents (these are the provider's own working papers on its guarantee; the report-contents list also seeds the daily/monthly report products), plus any format-agreement deadline the *owner* must hit; **owner response windows where silence is acceptance** — above all the window to dispute the provider's outage-responsibility attribution, which flows straight into the availability LDs. *Instrument these windows before anything else propagates: they are the only obligations where doing nothing costs money.* Then personnel, communication protocols, costs & settlement, and pointers to the other companions.

## Step 10 — Verification passes

- **Keyword scan**: obligation (*shall, must, responsible for, at its cost*), communication (*report, notify, notice, submit, consent, approval*), money (*pay, fee, liquidated damages, invoice, cap, refund*), time (*within, days, hours, promptly*). Every hit captured or consciously excluded; record the scan in the folder `log.md`.
- **Definitions cross-reference**: for each load-bearing term, look up every use; confirm the operating clauses are captured; add "used in" notes.

## Step 10b — Red flags → `red-flags.md`

Anything structurally against the owner — a metric measuring less than its name claims, an asymmetric adjustment, a condition ordinary operation will violate, a coverage gap against a sibling contract — as a numbered flag: **as written** (cited, page-verified), **why it bites**, **mitigations**. This is the file non-specialists read.

## Step 11 — Missing documents, close out

List every referenced document not in the repo, with why it matters and what cannot be computed without it — the **equipment supply contract** always matters (covered equipment, the date that starts every clock, the BOL capacity that anchors the degradation curve). Then: both indexes, folder `log.md`, `todo.md`, the definitions **roll-up into the master glossary in the same session**, and propagation (guarantee-matrix rows and calc sheets, warranty matrix, metrics tree, interface register, the outage tracker, `project_info.md`).

## Review checklist

- [ ] Full text extracted with `-layout`; surprising values page-verified; drafting defects recorded as written
- [ ] Every exhibit listed; reserved and unattached ones flagged
- [ ] Defined terms extracted with completeness check; not-held-document terms ❓; cross-contract collisions ⚠️
- [ ] Warranty scope, periods, exclusions **with carve-backs**, remedies, assignability, pass-through; owner-side conditions listed separately
- [ ] Each guarantee through the **seven questions**; full tables
- [ ] BOL headroom computed; reset/interpolation/permanent-adjustment rules found; the curve's absolute anchor located
- [ ] **Augmentation searched; presence or absence recorded as a finding**, with what substitutes and who carries the offtake gap
- [ ] Availability boundary, proration example, seasonal split, rebasing interaction
- [ ] Efficiency meter and aux treatment; LD formula transcribed
- [ ] Everything tested-but-not-guaranteed compared against the offtake agreement
- [ ] Exclusions per guarantee with carve-backs and caps; offtake asymmetry marked per event
- [ ] Liability cap, LD caps, carve-outs, security
- [ ] Every provider report with deadline; every **owner silence-is-acceptance window**
- [ ] Referenced documents not held; companions written; indexes, log, glossary roll-up, handoffs done
