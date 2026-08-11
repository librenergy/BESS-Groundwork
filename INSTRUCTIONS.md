---
type: Guide
title: Instructions
description: "How to work in a project clone of this toolkit: what lives where, how to find an answer, how to add source documents, how to build and edit documents, and the conventions that keep it consistent."
---

# Instructions

How to actually use this repository, for the people working on a project.

Everything here is **plain Markdown**. There is nothing to install, build, or run — GitHub, VS Code, Obsidian, or any text editor will do. The documents *are* the deliverable. An AI agent makes the work faster (see [§8](#8-working-with-an-ai-agent)), but nothing in this repo requires one.

If you read only one thing: **[§2 Find what you need](#2-find-what-you-need)** and **[§7 Conventions](#7-conventions-follow-these-when-you-edit)**.

---

## 1. What this repository is

A project's performance-engineering context in one place — the contracts, the topology, the measurement definitions, and the deliverables built from them. It exists so that when someone asks *"is the plant meeting its availability guarantee, and how do we prove it?"*, the answer is traceable from a number back to a clause.

It is organised in three layers:

| Layer | What it is | Where |
|:---|:---|:---|
| **1 — Project inputs** | The raw facts: key project data, plus every client-provided contract, spec, and drawing set, each reviewed into Markdown companions beside the original PDF. | [`project_info.md`](./project_info.md), [`Project_Documentation/`](./Project_Documentation/index.md) |
| **2 — Toolkit documents** | The engineering layer built *from* the inputs: the entity map, the registers, the guarantee and warranty matrices, the metrics, the procedures. One folder per document. | `Entity_Interaction_Map(EIM)/`, `Metrics_Tree(MT)/`, `Performance_Guarantee_Matrix(PGM)/`, … |
| **3 — Data products** | The recurring deliverables generated from layers 1 and 2: reports, exchanges, exports. | [`Data_Product(DP)/`](./Data_Product%28DP%29/index.md) |

Layer 2 anchors on the **Entity Interaction Map (EIM)** — the diagram of every company, contract, system, and data flow on the project. Every other document derives from it and references its node IDs, which is why most of them carry a *Source EIM version* header.

This is the **master template repo**: every document here is a pristine, unpopulated base. Work happens in a **per-project clone**, where each template is edited in place with real project content. See [README.md](./README.md) for the full resource index and the recommended build sequence, and [AGENTS.md](./AGENTS.md) for the AI-agent entry point.

---

## 2. Find what you need

| You want to know… | Go to |
|:---|:---|
| The headline project facts — capacity, counterparties, milestones, LD values | [`project_info.md`](./project_info.md) |
| What an acronym or term means on this project | [`Definitions_Taxonomy(DT)/definitions.md`](./Definitions_Taxonomy%28DT%29/definitions.md) — the master glossary |
| Exactly how one contract defines a term, in its own words | `Project_Documentation/<DOMAIN>/definitions.md` — the per-document extraction the glossary rolls up from |
| What a contract actually says | `Project_Documentation/<DOMAIN>/summary.md` — the reviewed extract; the source PDF sits beside it |
| Exact guarantee mechanics — formulas, exclusions, LDs | [`Performance_Guarantee_Matrix(PGM)/`](./Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) and its calculation sheets |
| How a metric is defined and which number is authoritative | [`Metrics_Tree(MT)/metrics-tree.md`](./Metrics_Tree%28MT%29/metrics-tree.md) |
| Who the counterparties are and how they connect | [`Entity_Interaction_Map(EIM)/`](./Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) |
| What data comes from where, over which protocol | [`Data_Interface_Register(DIR)/`](./Data_Interface_Register%28DIR%29/data-interface-register.md) |
| How outages get classified and counted | [`Data_Product(DP)/BESS_GADS/bess-gads.md`](./Data_Product%28DP%29/BESS_GADS/bess-gads.md) |
| What we owe the counterparty daily / monthly | [`Data_Product(DP)/`](./Data_Product%28DP%29/index.md) |
| The full document list with current status | [`index.md`](./index.md) |
| What changed recently | [`log.md`](./log.md), plus each folder's own `log.md` |
| What is still open | Each folder's `todo.md` |

**One trap worth knowing before you read anything else:** on a project with both an offtake agreement and an OEM service agreement, headline terms like *availability* and *round-trip efficiency* usually carry **two different contractual definitions** that are not derivable from each other. They are tracked as separate metrics and never merged. Terms with a ⚠️ in the glossary carry more than one meaning.

---

## 3. How to read a document

Every folder in layers 1–3 follows the same four-file shape. Once you know it, you can navigate any folder in the repo:

| File | Holds | Notes |
|:---|:---|:---|
| `<document-name>.md` | The document itself | Clean, formal, **established fact only** |
| `index.md` | What is in the folder, one bullet per file, plus current status | Start here when you open a folder |
| `log.md` | Change history, newest first, dated | All edit history lives here — never in the document body |
| `todo.md` | Open items as a checklist | Questions, conflicts, and pending decisions live here — never in the document body |

Some folders add a `notes.md` for draft findings and analysis that are not yet established fact.

Inside a document:

- The **header line** carries project, version, and last-updated date; most documents also carry a **Source EIM version**, which tells you which version of the entity map the document was built against.
- **❓** marks a value that is not yet known. **⚠️** marks a trap — a name collision, a conflicting figure, an assumption that will bite.
- Documents still labelled *Template* have not been populated for the project yet; they carry `{{PLACEHOLDER}}` values.

---

## 4. Adding a source document

New contract, spec, drawing set, or report? The goal is that nobody has to re-read the PDF: the review turns it into Markdown that is cheap to search and cites the clause when challenged.

1. **File it** under `Project_Documentation/<DOMAIN>/` — `PPA/`, `LTSA/`, `EPC/`, `GRID/`, `O&M/`, `OEM/`, `SCADA/`, `Design/`, `Asset_Management/`, `EMS/`, `Performance_Engineering/`. If a folder is collecting several unrelated documents, give each one its own subfolder.
2. **Review it** into companions beside the source file: a `summary.md` always, a **`definitions.md`** for any contract, and a `guarantees.md` whenever the document carries performance guarantees, warranties, or LDs. Cite clause and page for every claim; transcribe year-by-year tables in full rather than pointing at them.
3. **Roll the definitions up.** The `definitions.md` you just wrote holds what *this* document defines, in its own words — it is the source of record, not a competing glossary. Promote from it into the master glossary ([`Definitions_Taxonomy(DT)/definitions.md`](./Definitions_Taxonomy%28DT%29/definitions.md)) every term that travels beyond this document: acronyms, anything naming money, a guarantee, a measurement boundary, a clock or a deadline — and, mandatorily, **every word another contract defines differently** (⚠️, both meanings). Link each promoted entry back to the extraction it came from. Internal contract vocabulary stays in the folder. **Do this in the same session as the review** — an extraction that says one thing while the master still says another is the drift this two-tier structure exists to prevent.
4. **Index it** — add a bullet in the folder's `index.md` and in [`Project_Documentation/index.md`](./Project_Documentation/index.md), and log the review in the folder's `log.md`.
5. **Propagate** any new fact into [`project_info.md`](./project_info.md) and into the affected toolkit documents (a new guarantee → the Performance Guarantee Matrix; a new interface → the Data Interface Register; a new counterparty → the EIM).
6. **Record what you could not resolve** in the folder's `todo.md`, including any document the source references that the project does not hold.

Ask an agent to run the `project-document-review` skill and it will do all of it — *"review this PDF and add it to the index"*.

---

## 5. The build sequence — checklist

The order matters: each step consumes the output of the ones above it. Copy this into the project clone and tick as you go.

### Phase 1 — Inputs

Nothing below Phase 1 is worth starting until the contracts are in and reviewed. Each review follows [§4](#4-adding-a-source-document) and produces `summary.md` + `definitions.md` (+ `guarantees.md` where the document carries guarantees). Skill: `project-document-review`.

- [ ] **1. Fill `project_info.md`** — a starting point, not a finished document. `TBC` is a perfectly good value; it is the fastest orientation any later reader or agent gets.
- [ ] **2. Load the source documents** into their `Project_Documentation/<DOMAIN>/` folders.
- [ ] **3. Design documents** (if they exist) — sizing basis, augmentation plan. Sets the physical baseline everything else is measured against.
- [ ] **4. Offtake agreement** (`PPA/`) — the commercial spine: revenue mechanics, guarantees, usage limits, dispatch rights.
- [ ] **5. OEM service agreement** (`LTSA/`) — the supply-side guarantees that shadow the offtake ones.
- [ ] **6. Interconnection agreement** (`GRID/`) — telemetry and metering duties, outage obligations, operating envelope.
- [ ] **7. O&M agreement(s)** — feeds the RACI, the escalation plan, and the service-level rows of the guarantee matrix.
- [ ] **8. EPC contract** — workmanship warranties feed the Warranty Obligation Matrix.
- [ ] **9. SCADA sets + OEM equipment specs** — the Data Interface Register at step 16 depends on these; give them companions rather than mining them ad hoc.

### Phase 2 — Vocabulary and parties

- [ ] **10. Definitions & Taxonomy** (`definitions-taxonomy`) — roll every per-document `definitions.md` up into the master glossary, settle the ⚠️ collisions, then run the taxonomy session (canonical names, organization codes, ID prefixes). **Do this before anything below**, so every document that follows is written in one settled vocabulary.
- [ ] **11. Contact Register** — the named people and organizations behind the parties. No skill; fill the template directly.
- [ ] **12. Entity Interaction Map** (`eim-review-build`) — the anchor. **Everything below derives from it** and references its node IDs.

### Phase 3 — Obligations, roles, and measurement

- [ ] **13. Warranty Obligation Matrix** (`warranty-obligation-matrix`) — defect-style warranties, claim paths, owner-side conditions that keep cover valid. Needs steps 5 and 8.
- [ ] **14. Performance Guarantee Matrix** (`performance-guarantee-matrix`) — measured-performance guarantees, calculation methodologies, exclusion asymmetries.
- [ ] **15. RACI Matrix** (`raci-matrix`) — who is responsible and accountable for each event and process.
- [ ] **16. Data Interface Register** (`data-interface-register`) — systems, telemetry chains, protocols, integration points. Depends on step 9.
- [ ] **17. Metrics Tree & KPIs** (`metrics-tree`) — tree first, then the authoritative metric calculations, then the KPI targets. Depends on steps 14 and 16.
- [ ] **18. Escalation & Communication Plan** (`escalation-plan`) — who calls whom, severity levels, notification deadlines. Builds on steps 11 and 15.

### Phase 4 — Data products

Everything here needs defensible numbers from Phase 3 first. New products are scoped with `data-product`.

- [ ] **19. BESS GADS / outage tracking** (`bess-gads`) — the outage/derate event taxonomy and ledger. **Before the reports**, which read it.
- [ ] **20. Daily Performance Report** — availability under each measurement definition; the diagnostic instrument.
- [ ] **21. Monthly Performance Report** (`monthly-performance-report`) — the assembled owner/lender/IE pack.
- [ ] **22. Counterparty data exchange** — any recurring payload the offtake agreement obliges. Contractual, so not optional where it exists.
- [ ] **23. Settlement Reconciliation** (`settlement-reconciliation`) — the shadow-settlement or invoice walk.

### Notes on the order

- **GADS comes before the reports.** The daily and monthly reports both *read* the event ledger — availability, excused minutes, and commercial events all resolve against it. Build the reports first and you build them against unsettled event types, then rework them.
- **Contact Register before the EIM works.** It looks inverted, but the parties come from the contracts rather than the map, and the Contact Register is one of only two documents carrying no *Source EIM version* stamp.
- **Steps 3–9 can run in any order** internally, but all should finish before step 10 — the definitions roll-up is only as complete as the reviews feeding it.
- **Steps 3 and 12 can swap** where a reviewer already holds the whole picture: build the map from the interview and let the document reviews become the verification pass.

---

## 6. Building or updating a toolkit document

Each document in layers 2 and 3 is produced through a **facilitated Q&A session**: the questions needed to ground the document in the real project, asked one section at a time. The question sets live in [`.agents/skills/`](./.agents/skills/) as `SKILL.md` files, and the README's Resources tables map each document to its skill.

You can work either way:

- **With an agent** — ask for the document by name (*"build the Data Interface Register"*) and it runs the session, populating the file in place. See [§8](#8-working-with-an-ai-agent).
- **By hand** — open the matching `SKILL.md` and use it as an interview checklist and structure guide, then edit the document directly. The skills are readable prose; nothing in them requires an agent to execute.

Before you start any session:

- **Build in order.** The EIM comes first; everything else references it. The README's *Recommended sequence* gives the dependency order for the rest.
- **Read the glossary first** ([`definitions.md`](./Definitions_Taxonomy%28DT%29/definitions.md)) and use the established vocabulary. Any new acronym you introduce goes into the glossary in the same session.
- **Bring the sources.** A guarantee session needs the reviewed contract extract; a metrics session needs the Data Interface Register. Sessions grounded in "what we think we remember" are the ones that get rebuilt later.

When you finish: bump the version in the document header, write the folder `log.md` entry, move anything unresolved into `todo.md`, and update `index.md` if a file was added.

**When the EIM changes**, search the repo for the previous *Source EIM version* string to find every document that needs a review pass. Re-stamp only after you have actually checked the document against the new map.

---

## 7. Conventions — follow these when you edit

The repo follows [OKF v0.2](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md). Full detail is in [AGENTS.md](./AGENTS.md); this is what matters in practice:

1. **Frontmatter on every document.** Every `.md` file except `index.md`, `log.md`, and `todo.md` opens with YAML frontmatter carrying a non-empty `type` (plus `title` and `description`). Keep frontmatter and `index.md` descriptions **project-agnostic** — the project name belongs in the document body.
2. **Documents carry established fact.** Draft findings, analysis, and speculation go in a `notes.md`. If it is not confirmed, it does not go in the formal document.
3. **History in `log.md`, open items in `todo.md`.** No change-log tables or "open questions" sections inside document bodies.
4. **Update the indexes.** New document → a bullet in the folder `index.md` and, if it is significant, in the root [`index.md`](./index.md).
5. **Log what you change.** Detailed entry in the folder's `log.md`; the root `log.md` carries repo-wide or cross-cutting changes and pointers to folder milestones.
6. **Naming.** Document files are kebab-case (`data-interface-register.md`). Acronym suffixes belong to top-level folders only — a folder nested inside `Data_Product(DP)/` uses its plain name. Canonical names and codes are settled in [`taxonomy.md`](./Definitions_Taxonomy%28DT%29/taxonomy.md).
7. **Cite the source.** Any contractual number gets its clause or exhibit reference. A figure with no traceable source is a finding, not a fact.

**Project content stays in the clone; structural improvements come back here.** If a session produces a better convention, a template fix, or a sharper skill, port it to the master template repo so the next project starts from it. Project facts, populated documents, and `Project_Documentation/` content are never ported.

---

## 8. Working with an AI agent

Optional, but it is how most of the work here gets done. [`AGENTS.md`](./AGENTS.md) is the entry point for any agent; the skills in [`.agents/skills/`](./.agents/skills/) are the single source of truth regardless of which tool you use. Per-tool setup (Claude Code, Codex) is in the README's [Using with AI agents](./README.md#using-with-ai-agents) section — it is two minutes, and your local agent config is gitignored so it never touches the shared repo.

**What to ask for.** Name the document or the task; the matching skill runs itself:

- *"Build the RACI matrix"* — runs the facilitated session for that document.
- *"Review this PDF and add it to the index"* — runs the source-document review.
- *"What does the offtake agreement say about planned outages?"* — answers from the reviewed companions, with citations.
- *"The EIM changed to v0.7 — which documents need review?"* — sweeps the Source EIM version stamps.

**Review its output like a colleague's draft.** Two checks catch most of it: does every contractual number carry a clause reference, and did anything unconfirmed get written into the formal document instead of `notes.md` or `todo.md`?

---

## 9. Starting a new project clone

1. **Clone this repository** for the project and point `origin` at the project's own remote, keeping this repo as `upstream` so improvements can be compared later.
2. **Fill [`project_info.md`](./project_info.md)** with whatever is known — it is the fastest orientation any reader (or agent) gets, and `TBC` is a perfectly good value.
3. **Load the source documents** into `Project_Documentation/` and review them ([§4](#4-adding-a-source-document)). Do this before the first session, not after.
4. **Run the EIM session**, then work down the README's recommended sequence.
5. **Delete nothing.** Templates that are not yet relevant stay in place as templates — they are the prompt for the session that has not happened yet.
