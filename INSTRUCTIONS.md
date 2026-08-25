---
type: Guide
title: Instructions
description: "How to work in a project clone of this toolkit: what lives where, how to find an answer, how to add source documents, how to build and edit documents, and the conventions that keep it consistent."
---

# Instructions

How to actually use this repository, for the people working on a project.

Everything here is **plain Markdown**. There is nothing to install, build, or run: GitHub, VS Code, Obsidian, or any text editor will do. The documents *are* the deliverable. An AI agent makes the work faster (see [§8](#8-working-with-an-ai-agent)), but nothing in this repo requires one.

If you read only one thing: **[§2 Find what you need](#2-find-what-you-need)** and **[§7 Conventions](#7-conventions-follow-these-when-you-edit)**.

---

## 1. What this repository is

A project's performance-engineering context in one place: the contracts, the topology, the measurement definitions, and the deliverables built from them. It exists so that when someone asks *"is the plant meeting its availability guarantee, and how do we prove it?"*, the answer is traceable from a number back to a clause.

It is organised in three layers:

| Layer | What it is | Where |
|:---|:---|:---|
| **1. Project inputs** | The raw facts: key project data, plus every client-provided contract, spec, and drawing set, each reviewed into Markdown companions beside the original PDF. | [`project_info.md`](./project_info.md), [`Project_Documentation/`](./Project_Documentation/index.md) |
| **2. Toolkit documents** | The engineering layer built *from* the inputs: the entity map, the registers, the guarantee and warranty matrices, the metrics, the procedures. One folder per document. | `Entity_Interaction_Map(EIM)/`, `Metrics_Tree(MT)/`, `Performance_Guarantee_Matrix(PGM)/`, … |
| **3. Outputs** | What the project produces from layers 1 and 2: recurring **data products** (reports, exchanges, and exports generated on a fixed cadence), one-off **work products** (engineering studies and reviews ending in findings), and standing **SOPs** (procedures issued to the people who operate the plant). | [`Data_Product(DP)/`](./Data_Product%28DP%29/index.md), [`Work_Product(WP)/`](./Work_Product%28WP%29/index.md), [`Standard_Operating_Procedures(SOP)/`](./Standard_Operating_Procedures%28SOP%29/index.md) |

Layer 2 anchors on the **Entity Interaction Map (EIM)**: the diagram of every company, contract, system, and data flow on the project. Every other document derives from it and references its node IDs, which is why most of them carry a *Source EIM version* header.

This is the **master template repo**: every document here is a pristine, unpopulated base. Work happens in a **per-project clone**, where each template is edited in place with real project content. See [README.md](./README.md) for the full resource index and the recommended build sequence, and [AGENTS.md](./AGENTS.md) for the AI-agent entry point.

---

## 2. Find what you need

| You want to know… | Go to |
|:---|:---|
| The headline project facts: capacity, counterparties, milestones, LD values | [`project_info.md`](./project_info.md) |
| What an acronym or term means on this project | [`Definitions_Taxonomy(DT)/definitions.md`](./Definitions_Taxonomy%28DT%29/definitions.md), the master glossary |
| Exactly how one contract defines a term, in its own words | `Project_Documentation/<DOMAIN>/definitions.md`, the per-document extraction the glossary rolls up from |
| What a contract actually says | `Project_Documentation/<DOMAIN>/summary.md`, the reviewed extract; the source PDF sits beside it |
| Exact guarantee mechanics: formulas, exclusions, LDs | [`Performance_Guarantee_Matrix(PGM)/`](./Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) and its calculation sheets |
| How a metric is defined and which number is authoritative | [`Metrics_Tree(MT)/metrics-tree.md`](./Metrics_Tree%28MT%29/metrics-tree.md) |
| Who the counterparties are and how they connect | [`Entity_Interaction_Map(EIM)/`](./Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) |
| What data comes from where, over which protocol | [`Data_Interface_Register(DIR)/`](./Data_Interface_Register%28DIR%29/data-interface-register.md) |
| How outages get classified and counted | [`Data_Product(DP)/Outage_Tracker/outage-tracker.md`](./Data_Product%28DP%29/Outage_Tracker/outage-tracker.md) |
| What we owe the counterparty daily / monthly | [`Data_Product(DP)/`](./Data_Product%28DP%29/index.md) |
| A study or review the project has run, or should run | [`Work_Product(WP)/`](./Work_Product%28WP%29/index.md), the catalogue of recurring studies plus the completed ones |
| The procedure an operator or counterparty works from | [`Standard_Operating_Procedures(SOP)/`](./Standard_Operating_Procedures%28SOP%29/index.md), plus its issuance log |
| The full document list with current status | [`index.md`](./index.md) |
| What changed recently | [`log.md`](./log.md), plus each folder's own `log.md` |
| What is still open | Each folder's `todo.md`; the repo-wide working backlog is the root [`todo.md`](./todo.md) |

**One trap worth knowing before you read anything else:** on a project with both an offtake agreement and an OEM service agreement, headline terms like *availability* and *round-trip efficiency* usually carry **two different contractual definitions** that are not derivable from each other. They are tracked as separate metrics and never merged. Terms with a ⚠️ in the glossary carry more than one meaning.

---

## 3. How to read a document

Every folder in layers 1–3 follows the same four-file shape. Once you know it, you can navigate any folder in the repo:

| File | Holds | Notes |
|:---|:---|:---|
| `<document-name>.md` | The document itself | Clean, formal, **established fact only** |
| `index.md` | What is in the folder, one bullet per file, plus current status | Start here when you open a folder |
| `log.md` | Change history, newest first, dated | All edit history lives here, never in the document body |
| `todo.md` | Open items as a checklist | Questions, conflicts, and pending decisions live here, never in the document body |

Some folders add a `notes.md` for draft findings and analysis that are not yet established fact.

Inside a document:

- The **header line** carries project, version, and last-updated date; most documents also carry a **Source EIM version**, which tells you which version of the entity map the document was built against.
- **❓** marks a value that is not yet known. **⚠️** marks a trap: a name collision, a conflicting figure, an assumption that will bite.
- Documents still labelled *Template* have not been populated for the project yet; they carry `{{PLACEHOLDER}}` values.

---

## 4. Adding a source document

New contract, spec, drawing set, or report? The goal is that nobody has to re-read the PDF: the review turns it into Markdown that is cheap to search and cites the clause when challenged.

1. **File it** under `Project_Documentation/<DOMAIN>/`: `PPA/`, `BSA/`, `LTSA/`, `EPC/`, `GRID/`, `O&M/`, `OEM/`, `SCADA/`, `Design/`, `Asset_Management/`, `EMS/`, `Performance_Engineering/`. If a folder is collecting several unrelated documents, give each one its own subfolder.
2. **Review it** into companions beside the source file: a `summary.md` always, a **`definitions.md`** for any contract, and a `guarantees.md` whenever the document carries performance guarantees, warranties, or LDs. Cite clause and page for every claim; transcribe year-by-year tables in full rather than pointing at them.
3. **Roll the definitions up.** The `definitions.md` you just wrote holds what *this* document defines, in its own words. It is the source of record, not a competing glossary. Promote from it into the master glossary ([`Definitions_Taxonomy(DT)/definitions.md`](./Definitions_Taxonomy%28DT%29/definitions.md)) every term that travels beyond this document: acronyms, anything naming money, a guarantee, a measurement boundary, a clock or a deadline. Mandatorily, also **every word another contract defines differently** (⚠️, both meanings). Link each promoted entry back to the extraction it came from. Internal contract vocabulary stays in the folder. **Do this in the same session as the review.** An extraction that says one thing while the master still says another is the drift this two-tier structure exists to prevent.
4. **Index it.** Add a bullet in the folder's `index.md` and in [`Project_Documentation/index.md`](./Project_Documentation/index.md), and log the review in the folder's `log.md`.
5. **Propagate** any new fact into [`project_info.md`](./project_info.md) and into the affected toolkit documents (a new guarantee → the Performance Guarantee Matrix; a new interface → the Data Interface Register; a new counterparty → the EIM).
6. **Record what you could not resolve** in the folder's `todo.md`, including any document the source references that the project does not hold.

Ask an agent to run the `project-document-review` skill and it will do all of it: *"review this PDF and add it to the index"*.

---

## 5. The build sequence checklist

The order matters: each step consumes the output of the ones above it. Copy this into the project clone and tick as you go.

### Phase 1: Inputs

Nothing below Phase 1 is worth starting until the contracts are in and reviewed. Each review follows [§4](#4-adding-a-source-document) and produces `summary.md` + `definitions.md` (+ `guarantees.md` where the document carries guarantees). Skill: `project-document-review`.

- [ ] **1. Fill `project_info.md`.** A starting point, not a finished document. `TBC` is a perfectly good value; it is the fastest orientation any later reader or agent gets.
- [ ] **2. Load the source documents** into their `Project_Documentation/<DOMAIN>/` folders.
- [ ] **3. Design documents** (if they exist): sizing basis, augmentation plan. Sets the physical baseline everything else is measured against.
- [ ] **4. Offtake agreement** (`PPA/`). The commercial spine: revenue mechanics, guarantees, usage limits, dispatch rights.
- [ ] **5. Battery supply agreement** (`BSA/`). What was actually bought: quantity, ratings, the defect and serial-defect warranty, and the commissioning dates that start the service agreement's clocks. **Review it before the service agreement**: the service agreement's formulas reference capacities this document defines.
- [ ] **6. OEM service agreement** (`LTSA/`): the supply-side guarantees that shadow the offtake ones.
- [ ] **7. Interconnection agreement** (`GRID/`): telemetry and metering duties, outage obligations, operating envelope.
- [ ] **8. O&M agreement(s).** Feeds the RACI (including its escalation sections) and the service-level rows of the guarantee matrix.
- [ ] **9. EPC contract.** Workmanship warranties feed the Warranty Obligation Matrix.
- [ ] **10. SCADA sets + OEM equipment specs.** The Data Interface Register at step 17 depends on these; give them companions rather than mining them ad hoc.

### Phase 2: Vocabulary and parties

- [ ] **11. Definitions & Taxonomy** (`definitions-taxonomy`). Roll every per-document `definitions.md` up into the master glossary, settle the ⚠️ collisions, then run the taxonomy session (canonical names, organization codes, ID prefixes). **Do this before anything below**, so every document that follows is written in one settled vocabulary.
- [ ] **12. Contact Register.** The named people and organizations behind the parties. No skill; fill the template directly.
- [ ] **13. Entity Interaction Map** (`eim-review-build`): the anchor. **Everything below derives from it** and references its node IDs.

### Phase 3: Obligations, roles, and measurement

- [ ] **14. Warranty Obligation Matrix** (`warranty-obligation-matrix`): defect-style warranties, claim paths, owner-side conditions that keep cover valid. Needs steps 5, 6 and 9; the supply agreement carries the defect warranty.
- [ ] **15. Performance Guarantee Matrix** (`performance-guarantee-matrix`): measured-performance guarantees, calculation methodologies, exclusion asymmetries.
- [ ] **16. RACI Matrix** (`raci-matrix`): who is responsible and accountable for each event and process, plus the escalation & communication sections (who calls whom, severity levels, notification deadlines; they also build on step 12).
- [ ] **17. Data Interface Register** (`data-interface-register`): systems, telemetry chains, protocols, integration points. Depends on step 10.
- [ ] **18. Metrics Tree & KPIs** (`metrics-tree`): tree first, then the authoritative metric calculations, then the KPI targets. Depends on steps 15 and 17.

### Phase 4: Data products

Everything here needs defensible numbers from Phase 3 first. New products are scoped with `data-product`.

- [ ] **19. Outage Tracker** (`outage-tracker`): the outage/derate event taxonomy and ledger. **Before the reports**, which read it.
- [ ] **20. Daily Performance Report.** Availability under each measurement definition; the diagnostic instrument.
- [ ] **21. Monthly Performance Report** (`monthly-performance-report`): the assembled owner/lender/IE pack.
- [ ] **22. Counterparty data exchange.** Any recurring payload the offtake agreement obliges. Contractual, so not optional where it exists.
- [ ] **23. Settlement Reconciliation** (`settlement-reconciliation`): the shadow-settlement or invoice walk.

### Alongside the sequence: studies and procedures

Two output categories are **trigger-driven, not sequenced**, so they carry no step numbers:

- **Work products** ([`Work_Product(WP)/`](./Work_Product%28WP%29/index.md)). The one-off engineering studies and reviews the project should run: reliability study, spare parts review, failover test, telemetry readiness assessment, performance test review, augmentation review, LD exposure review, RCA. The category index carries the recurring catalogue; prune it to what the project actually needs. Most are run once during onboarding and revisited on a trigger (an augmentation, an amendment, a significant event). The **telemetry readiness assessment** is usually the first one worth running, since it gates every metric in Phase 4.
- **SOPs** ([`Standard_Operating_Procedures(SOP)/`](./Standard_Operating_Procedures%28SOP%29/index.md)). Standing procedures issued to the people who operate the plant. They mostly fall *out* of other work rather than being scheduled: a study concluding "the operator should do X when Y" hands off an SOP, every escalation path in the RACI needs a procedure behind it, and the owner-side warranty conditions in the WOM are only met if somebody follows one. Unlike everything else in the repo, an SOP is a **controlled document** (issued, acknowledged, revised), so its folder tracks issuances in `log.md`.

The boundary rule between the three output types: a study that ends in findings is a **work product**; if it concludes "track this number monthly" it spawns a **data product**; if it concludes "someone should do X when Y" it spawns an **SOP**.

### Notes on the order

- **The Outage Tracker comes before the reports.** The daily and monthly reports both *read* the event ledger: availability, excused minutes, and commercial events all resolve against it. Build the reports first and you build them against unsettled event types, then rework them.
- **Contact Register before the EIM works.** It looks inverted, but the parties come from the contracts rather than the map, and the Contact Register is one of only two documents carrying no *Source EIM version* stamp.
- **Steps 3–10 can run in any order** internally, but all should finish before step 11; the definitions roll-up is only as complete as the reviews feeding it.
- **Steps 3 and 13 can swap** where a reviewer already holds the whole picture: build the map from the interview and let the document reviews become the verification pass.

---

## 6. Building or updating a toolkit document

Each toolkit document and data product is produced through a **facilitated Q&A session**: the questions needed to ground the document in the real project, asked one section at a time. The question sets live in [`.agents/skills/`](./.agents/skills/) as `SKILL.md` files, and the README's Resources tables map each document to its skill.

You can work either way:

- **With an agent.** Ask for the document by name (*"build the Data Interface Register"*) and it runs the session, populating the file in place. See [§8](#8-working-with-an-ai-agent).
- **By hand.** Open the matching `SKILL.md` and use it as an interview checklist and structure guide, then edit the document directly. The skills are readable prose; nothing in them requires an agent to execute.

Before you start any session:

- **Build in order.** The EIM comes first; everything else references it. The README's *Recommended sequence* gives the dependency order for the rest.
- **Read the glossary first** ([`definitions.md`](./Definitions_Taxonomy%28DT%29/definitions.md)) and use the established vocabulary. Any new acronym you introduce goes into the glossary in the same session.
- **Bring the sources.** A guarantee session needs the reviewed contract extract; a metrics session needs the Data Interface Register. Sessions grounded in "what we think we remember" are the ones that get rebuilt later.

When you finish: bump the version in the document header, write the folder `log.md` entry, move anything unresolved into `todo.md`, and update `index.md` if a file was added.

**When the EIM changes**, search the repo for the previous *Source EIM version* string to find every document that needs a review pass. Re-stamp only after you have actually checked the document against the new map.

---

## 7. Conventions: follow these when you edit

The repo follows [OKF v0.2](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md). Full detail is in [AGENTS.md](./AGENTS.md); this is what matters in practice:

1. **Frontmatter on every document.** Every `.md` file except `index.md`, `log.md`, and `todo.md` opens with YAML frontmatter carrying a non-empty `type` (plus `title` and `description`). Keep frontmatter and `index.md` descriptions **project-agnostic**: the project name belongs in the document body.
2. **Documents carry established fact.** Draft findings, analysis, and speculation go in a `notes.md`. If it is not confirmed, it does not go in the formal document.
3. **History in `log.md`, open items in `todo.md`.** No change-log tables or "open questions" sections inside document bodies.
4. **Update the indexes.** New document → a bullet in the folder `index.md` and, if it is significant, in the root [`index.md`](./index.md).
5. **Log what you change.** Detailed entry in the folder's `log.md`; the root `log.md` carries repo-wide or cross-cutting changes and pointers to folder milestones.
6. **Naming.** Document files are kebab-case (`data-interface-register.md`). Acronym suffixes belong to top-level folders only; a folder nested inside `Data_Product(DP)/` uses its plain name. Canonical names and codes are settled in [`taxonomy.md`](./Definitions_Taxonomy%28DT%29/taxonomy.md).
7. **Cite the source.** Any contractual number gets its clause or exhibit reference. A figure with no traceable source is a finding, not a fact.

**Project content stays in the clone; structural improvements come back here.** If a session produces a better convention, a template fix, or a sharper skill, port it to the master template repo so the next project starts from it. Project facts, populated documents, and `Project_Documentation/` content are never ported.

---

## 8. Working with an AI agent

Optional, but it is how most of the work here gets done. [`AGENTS.md`](./AGENTS.md) is the entry point for any agent; the skills in [`.agents/skills/`](./.agents/skills/) are the single source of truth regardless of which tool you use. Per-tool setup (Claude Code, Codex) is in the README's [Using with AI agents](./README.md#using-with-ai-agents) section. It takes two minutes, and your local agent config is gitignored so it never touches the shared repo.

**How it answers questions.** Agents work a fixed retrieval order (the "Answering questions" section of [`AGENTS.md`](./AGENTS.md)): a knowledge-graph index if the clone has one, then the Markdown companions around it, then the **source PDFs those companions were extracted from**, and only then the web. Web search is last and is barred for project facts, so a contract term or equipment rating is either quoted from your documents with a citation or reported as an open item. If you are ever told a project fact "is not in the repo", ask whether the source document itself was opened.

### Optional: a local knowledge-graph index

An index over this repo that lets an agent find the right document fast. Optional (everything works without it, just by `grep`) and **per-user**: `graphify-out/` is gitignored, so it never commits and never syncs.

**1. Install.** One time, machine-wide; not part of this repo:

```sh
brew install uv                    # macOS; or: curl -LsSf https://astral.sh/uv/install.sh | sh
uv tool install graphifyy          # note: package is "graphifyy", the command is "graphify"
graphify install                   # installs the /graphify skill into your agent
graphify --version                 # verify
```

No API key is needed. Documents are read by whichever agent you already use.

**2. Build.** From the repo root:

```sh
graphify .
```

Minutes, not hours. It reads every Markdown file and source PDF and writes `graphify-out/` (graph, an HTML viewer, and a `GRAPH_REPORT.md`).

**3. Keep it current.** This differs from a code repo, because **this toolkit is Markdown and PDFs, with no source code**:

- **`graphify update` and the post-commit hook (`graphify hook install`) only re-read *code* files.** In a documentation repo they do nothing useful. Do not rely on them.
- To refresh after you add or edit documents, **ask your agent to update the graph** ("update the graph index"). A SHA256 cache means only changed files are re-read, so a refresh is far cheaper than the first build.
- Re-running `graphify .` does a full rebuild, also cache-backed.
- Optional: `graphify . --watch` in a spare terminal flags when a document re-pass is due; `graphify check-update` reports whether one is pending.

**Then just ask.** Do not type a slash command. Ask the question in plain English and the agent follows the retrieval order on its own.

Two cautions:

- **The graph is an index, not a source.** It tells you which document holds the answer; the number still comes from the Markdown companion, or the source PDF behind it. Never cite the graph.
- **Never run `graphify codex install`.** It appends tool-specific boilerplate to `AGENTS.md`, a synced method file, which would propagate to the master template and every other clone.

**What to ask for.** Name the document or the task; the matching skill runs itself:

- *"Build the RACI matrix"*: runs the facilitated session for that document.
- *"Review this PDF and add it to the index"*: runs the source-document review.
- *"What does the offtake agreement say about planned outages?"*: answers from the reviewed companions, with citations.
- *"The EIM changed to v0.7; which documents need review?"*: sweeps the Source EIM version stamps.

**Review its output like a colleague's draft.** Two checks catch most of it: does every contractual number carry a clause reference, and did anything unconfirmed get written into the formal document instead of `notes.md` or `todo.md`?

---

## 9. Starting a new project clone

1. **Clone this repository** for the project and point `origin` at the project's own remote, keeping this repo as `upstream`. Later template updates are pulled with the **`template-sync`** skill (method files sync three-way; populated documents are never touched); improvements flow back by the genericize-and-port workflow, checked with **`genericity-audit`**.
2. **Fill [`project_info.md`](./project_info.md)** with whatever is known. It is the fastest orientation any reader (or agent) gets, and `TBC` is a perfectly good value.
3. **Load the source documents** into `Project_Documentation/` and review them ([§4](#4-adding-a-source-document)). Do this before the first session, not after.
4. **Run the EIM session**, then work down the README's recommended sequence.
5. **Delete nothing.** Templates that are not yet relevant stay in place as templates; they are the prompt for the session that has not happened yet.
