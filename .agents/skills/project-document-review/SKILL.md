---
name: project-document-review
title: Project Document Review — Source Document to Indexed Markdown
type: Skill
description: Review a client-provided source document (PDF, Word, Excel drawing set) and turn it into indexed, agent-ingestible Markdown in Project_Documentation. Use whenever the user drops a new contract, spec, drawing set, or report into the repo and asks to review, summarize, or index it — phrases like "review this PDF", "summarize the tolling agreement", "add this to the index", "what does this contract say". Produces a summary.md (always) and a guarantees.md extraction (when the document carries performance guarantees, warranties, or LDs), then hands guarantee content off to the performance-guarantee-matrix and warranty-obligation-matrix skills.
---

# Project Document Review — Source Document to Indexed Markdown

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Source documents (PDFs, DOCX, drawing sets) are where the facts live, but they are slow and expensive to re-read. This skill turns each one into **companion Markdown files that are cheap to ingest later**, wired into the OKF index structure, with the source PDF kept as the citable original. The test of a good review: six months from now, an agent answers questions about the document from the companions alone, and can cite the exact page/clause when challenged.

## Where documents live

- `Project_Documentation/<DOMAIN>/` — one folder per contract/document family: `PPA/` (offtake), `LTSA/`, `EPC/`, `GRID/` (interconnection), `O&M/`, `OEM/`, `SCADA/`, `Design/`, etc.
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
- References to documents **not in the repo** (an SPA, an exhibit "attached separately") — each becomes an open item, never a guess.

### Step 4 — Write the companions (alongside the source file)

Every companion carries OKF frontmatter and cites clause/section/exhibit references so claims are checkable.

1. **`summary.md`** (always, `type: summary`): the key facts, front-loaded — parties, dates, term, headline commercial and technical values, measurement basis, and pointers into the document. Write it for later ingestion: tables for enumerable facts, a "at a glance" block answering the questions the user actually asked, links to the other companions.
2. **`guarantees.md`** (when the document contains performance guarantees, warranties, or LDs; `type: extraction`): the complete guarantee extraction — levels, full tables, exact formulas, every exclusion/excuse condition, test protocols, reporting obligations, LD rates and caps (as reference). Follow the calc-sheet content standard from the `performance-guarantee-matrix` skill.
3. **Other extractions as warranted** (`type: analysis`): telemetry mappings, interface lists, test procedures — whatever this document uniquely contributes. Import any pre-existing external analysis of the document into the folder (with frontmatter) rather than leaving it outside the repo, and cross-check it against the source — record what was confirmed and what was corrected.

### Step 5 — Index and log
- Per-folder `index.md`: create it if missing; list the source document (with pages, execution date, review date) and every companion, one line each.
- `Project_Documentation/index.md`: update the inventory row — description, review status ("Reviewed YYYY-MM-DD; guarantees extracted"), and any moved/removed files.
- Root `log.md`: one entry per review, with the headline findings.

### Step 6 — Propagate
A review isn't done when the summary exists — push the facts to where they're used:

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

1. Source document correctly placed, with `summary.md` (+ `guarantees.md` / analyses as applicable) beside it.
2. Per-folder `index.md` and the `Project_Documentation/index.md` inventory updated; `log.md` entry written.
3. `project_info.md` facts and open items updated.
4. Handoff list for the matrix skills, plus flagged contradictions and missing referenced documents.
