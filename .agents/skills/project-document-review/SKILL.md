---
name: project-document-review
title: Project Document Review — Source Document to Indexed Markdown
type: Skill
description: Review a client-provided source document (PDF, Word, Excel drawing set) and turn it into indexed, agent-ingestible Markdown in Project_Documentation. Use whenever the user drops a new contract, spec, drawing set, or report into the repo and asks to review, summarize, or index it — phrases like "review this PDF", "summarize the tolling agreement", "add this to the index", "what does this contract say". Produces the domain's companion set beside the source file (summary, definitions, guarantees, operations-and-maintenance, outage-management, telemetry, red-flags as applicable), rolls the definitions up into the master glossary, and hands guarantee content off to the performance-guarantee-matrix and warranty-obligation-matrix skills.
---

# Project Document Review — Source Document to Indexed Markdown

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Source documents (PDFs, DOCX, drawing sets) are where the facts live, but they are slow and expensive to re-read. This skill turns each one into **companion Markdown files that are cheap to ingest later**, wired into the OKF index structure, with the source PDF kept as the citable original. The test of a good review: six months from now, an agent answers questions about the document from the companions alone, and can cite the exact page/clause when challenged.

## Where documents live

- `Project_Documentation/<DOMAIN>/` — one folder per contract/document family: `PPA/` (offtake), `LTSA/`, `EPC/`, `GRID/` (interconnection), `O&M/`, `OEM/`, `SCADA/`, `Design/`, etc.
- **Every document folder carries an `instructions.md` — read it first; it is the starting point for how that folder's documents get reviewed.** It holds the domain-specific method (which companions to produce, what goes in each, the verification passes) layered on the mechanical workflow below. If the folder has none yet, write one as part of the review, modelled on the `PPA/` or `LTSA/` examples, so the next review of that domain starts from it.
- If a file is misfiled (e.g., the LTSA sitting under `OEM/`), move it to the right domain folder and update both indexes.
- **One document family per folder.** When a folder accumulates multiple unrelated documents, create a subfolder per document (named after the document) so each gets its own `index.md` and companions without collisions. A folder with a single document keeps its companions at folder level: `summary.md`, `guarantees.md` — fixed names, so downstream skills can find them.

## Review workflow

### Step 1 — Place and classify
Confirm the file is in the right domain folder (move it if not). Note filename, page count, execution status (draft / executed / rev number), and date.

### Step 2 — Extract text, keep the images honest
Extract the full text (`pdftotext` or equivalent) to a scratch file for grep-driven navigation. For any **surprising value** — a number that breaks a monotonic table, an OCR-suspect figure, a map or drawing — render the actual PDF page and verify visually before recording it. Never let an extraction artifact into a companion file. (Example: a late-year guarantee value that breaks an otherwise monotonic table — verify against the signed page image and footnote it as a drafting quirk rather than silently "correcting" it.)

### Step 3 — Targeted read
Read the cover/key terms, table of contents, and definitions first; then grep to the sections that matter for this repo's purpose:

- Parties (exact legal entities — they resolve ownership questions), effective/execution dates, term.
- Commercial structure: prices, fees, escalation, payment mechanics.
- Technical parameters: capacities, boundaries (POI/POM), equipment, operating limits.
- **Guarantees, warranties, LDs, and their calculation mechanics** (formulas, evaluation periods, exclusions, test protocols) — transcribe year-by-year tables **in full**, never "see table".
- Obligations with deadlines: notices, reports, milestones.
- References to documents **not in the repo** (an equipment supply agreement, an exhibit "attached separately") — each becomes an open item, never a guess.

**For contracts, extract the defined terms into `definitions.md` before anything else.** Capitalized defined terms span exhibits and sibling agreements and carry the operative meaning — a guaranteed level is usually a defined term referenced everywhere else. Flag terms defined in documents not held, and ⚠️ terms that collide with another project contract's definition.

### Step 4 — Write the companions (alongside the source file)

Every companion carries OKF frontmatter and cites clause/section/exhibit references so claims are checkable. **The folder's `instructions.md` names the companion set for that domain**; the standard set for a contract:

1. **`summary.md`** (always, `type: summary`): the key facts, front-loaded — parties, dates, term, **all** fees, measurement basis — plus the generic sections that every contract has and every review forgets: **reporting obligations** (each report owed, its deadline and contents), **personnel/organization** (named representatives, or an explicit note that none are named), **communication protocols** (how to raise a claim, request a test, dispute an invoice — channel, form, deadline), and **costs & settlement** (fee flow, cost triggers, payment mechanics). Links to every other companion.
2. **`definitions.md`** (contracts, `type: extraction`): all defined terms, one line each with source clause; a usage map for the load-bearing terms; terms defined in documents not held flagged ❓. This is the **Tier 1** definitions file — the source of record for what *this* document defines, in its own words. It does not replace the master glossary; it feeds it (Step 6).
3. **`guarantees.md`** (when the document contains guarantees, warranties, or LDs; `type: extraction`): everything the party guarantees or must do. For a BESS contract the mandatory sections are **capacity, availability, round-trip efficiency, excused events, operating conditions, LDs and caps, warranty obligations, testing**. Levels, full tables, exact formulas, every exclusion. Follow the calc-sheet content standard from the `performance-guarantee-matrix` skill.
4. **`operations-and-maintenance.md`** (service and O&M-bearing documents, `type: extraction`): PM schedule, corrective maintenance, monitoring and response times, coordination duties (site access, isolation, blackout windows), personnel and subcontracting, records.
5. **`outage-management.md`** (offtake and grid agreements, `type: extraction`): who declares and manages outages, notice regimes with deadlines, an explicit **excused-vs-charged table by cause**, attribution control and the dispute clock, and the system of record. Any notice-conditioned excuse belongs here — a deadline that converts an excused outage into a charged one is an operating procedure, not a legal footnote.
6. **`telemetry.md`** (documents defining data or telemetry, `type: analysis`): points and signals with **measured vs calculated** marked for each, update rates, interfaces, data ownership/access/sharing, and the historian tag set needed to shadow the guarantees. Calculated points whose formula the contract leaves unstated are findings.
7. **`red-flags.md`** (`type: analysis`): the document's structurally problematic features, written for the person who has *not* read the contract — each flag: the clause **as written** (cited, page-image-verified), **why it bites** (the realistic failure scenario), and **mitigations**. Candidates: metrics that measure less than their name claims, one-sided adjustments, conditions ordinary operation will violate, coverage gaps against sibling contracts, numbers the counterparty calculates.

**Verification passes before closing** (record both in the folder `log.md`):

- **Keyword scan** — grep the full text for obligation (*shall, must, responsible for*), communication (*report, notify, notice, submit, consent, deemed*), money (*pay, fee, liquidated damages, invoice, cap, deduction*) and time (*within, days, hours, promptly*) keywords; confirm every hit is captured in a companion or consciously excluded. This is what catches fees outside the fee table.
- **Definitions cross-reference** — for each load-bearing defined term, look up every use in the document and confirm the operating clauses are captured; add "used in" notes to `definitions.md`.

### Step 5 — Index and log
- Per-folder `index.md`: create it if missing; list the source document (with pages, execution date, review date) and every companion, one line each.
- `Project_Documentation/index.md`: update the inventory row — description, review status ("Reviewed YYYY-MM-DD; guarantees extracted"), and any moved/removed files.
- Root `log.md`: one entry per review, with the headline findings.

### Step 6 — Propagate
A review isn't done when the summary exists — push the facts to where they're used:

- **Definitions roll-up** (do this first — later handoffs depend on the vocabulary being settled): every term this review added to or changed in the folder's `definitions.md` gets promoted into the master glossary `Definitions_Taxonomy(DT)/definitions.md` **in the same session**, per the promotion rule in the `definitions-taxonomy` skill — acronyms, terms used outside this document, terms naming money/guarantees/boundaries/clocks/deadlines, and **every collision with a term another contract defines differently** (⚠️, both meanings, mandatory). Link each promoted entry's Source cell back to this `definitions.md` and clause. Internal contract vocabulary stays in the folder. A re-review that *corrects* a meaning corrects the master entry too.
- **`project_info.md`**: update confirmed facts (with `# source` comments), delete resolved open items, add new ones (especially missing referenced documents).
- **Guarantee content** → hand off to the `performance-guarantee-matrix` skill (measured-performance guarantees → inventory rows + calc sheets) and the `warranty-obligation-matrix` skill (defect warranties). When the new document sits on the other side of an existing guarantee (offtake vs supplier), update the exclusions-asymmetry analysis.
- **Cross-document checks**: compare against already-reviewed documents — matching addresses, conflicting dates, back-to-back guarantee margins. Contradictions are findings, not noise; surface them.
- Note any populated satellite documents whose source facts this document changes (EIM_VERSION-style review flags).

## Facilitation guidance

- **Numbers are quotes.** Every figure in a companion must be traceable to a page/clause; when derived (unit conversions, annualizations), show the arithmetic in a comment or parenthetical.
- Distinguish what the document **says** from what is **inferred** — mark inferences and ambiguities explicitly (e.g., grid-outage treatment "not addressed — ambiguity to resolve").
- Companion files are for *this* document; cross-document synthesis belongs in the satellite documents (matrices, registers) — link, don't duplicate.
- Keep summaries selective: what a reader needs to act, not a table of contents restated. The full detail lives in `guarantees.md` and the source itself.

## Outputs

1. Source document correctly placed, with the domain's companion set beside it (`summary.md` always; `definitions.md`, `guarantees.md`, `operations-and-maintenance.md`, `outage-management.md`, `telemetry.md`, `red-flags.md` as applicable), and the folder's `instructions.md` created if it was missing.
2. Per-folder `index.md` and the `Project_Documentation/index.md` inventory updated; `log.md` entry written.
3. `project_info.md` facts and open items updated.
4. Handoff list for the matrix skills, plus flagged contradictions and missing referenced documents.
