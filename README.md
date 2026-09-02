---
type: Index
title: BESS Groundwork
description: "Operational ontology for BESS performance engineering: the foundation layer a project lays before it can measure, report, or enforce anything."
---

# BESS Groundwork

**Operational ontology for BESS performance engineering.**

Groundwork is what you lay *before* performance engineering or asset management can start on a project. Before anyone can defend an availability number, contest a liquidated damage, or trust a dashboard, the project needs a settled account of itself: who the parties are, what each contract guarantees and on what formula, which system emits which signal, what a "battery string" is and what it is called, and which metric answers to which agreement. That account is the plant's operational ontology, and building it is the first real piece of work on any asset.

This repository is the method and the templates for building it: an entity map, the satellite registers and matrices that derive from it, a ratified vocabulary, a metrics layer, and the recurring data products that fall out the far end. Each document is built through a facilitated Q&A session with an AI agent, or filled by hand from the same questions.

BESS-first by design, and the depth of the taxonomy, the outage model, and the guarantee structures reflects that. The method itself is asset-agnostic: it transfers to PV, wind, and hybrid plants, and to any asset carrying multiple subcontracts, layered entity relationships, and matrixed guarantee and warranty structures.

## Purpose

The groundwork exists to support:

- **Data architecture**: Topology mapping, signal inventories, and data flow documentation.
- **Metrics design**: Grounded definitions that trace back to real sources, sinks, and contracts.
- **Workflow context**: End-to-end chains from alarm to action, including human and system handoffs.
- **AI agent context**: Structured topology and ownership boundaries for LLM-based analysis and automation.
- **Contract review**: Mapping LTSA scopes, SLA boundaries, and warranty obligations to actual data flows and system interfaces.

## How the repository is organised

Three layers, each built from the one below it:

| Layer | What it holds | Where |
|:---|:---|:---|
| **1: Project inputs** | The raw facts: key project data, plus every client-provided contract, spec, and drawing set, each reviewed into Markdown companions (`summary.md`, `definitions.md`, `guarantees.md`) beside the original file. | [`project_info.md`](./project_info.md), [`Project_Documentation/`](./Project_Documentation/index.md) |
| **2: Toolkit documents** | The engineering layer built *from* the inputs: the entity map, the registers, the guarantee and warranty matrices, the metrics layer, the operating procedures. One folder per document. | The document folders below |
| **3: Outputs** | What the project produces from layers 1 and 2, in three kinds distinguished by cadence and audience: **data products** (recurring deliverables generated on a fixed cadence: reports, counterparty exchanges, exports), **work products** (point-in-time engineering studies and reviews ending in findings), and **standard operating procedures** (standing step-by-step procedures issued to operating parties, revised and acknowledged as controlled documents). | [`Data_Product(DP)/`](./Data_Product%28DP%29/index.md), [`Work_Product(WP)/`](./Work_Product%28WP%29/index.md), [`Standard_Operating_Procedures(SOP)/`](./Standard_Operating_Procedures%28SOP%29/index.md) |

Every folder follows the same shape: the document itself (established fact only), an `index.md` (contents plus status), a `log.md` (change history), and a `todo.md` (open items). Draft findings and analysis live in a `notes.md` where one is needed, so the formal documents stay clean.

## How to use it

**[INSTRUCTIONS.md](./INSTRUCTIONS.md) is the working guide**: how to stand up a project clone, find an answer, add a source document, run a build session, and follow the conventions. This README is the index and the build sequence.

Everything in layer 2 anchors on the project **Entity Interaction Map (EIM)**: a Mermaid diagram of every entity (companies, contracts, assets, data systems) and interaction on a BESS project. The satellite documents all derive from it and reference its node IDs.

Each document is built through a facilitated Q&A session driven by a matching skill in `.agents/skills/`. Ask the agent to "build the EIM for [project]" (or any satellite document) and it will run the session, populate the template in place, bumping the version in its header. The skills are readable prose; they also work as interview checklists if you are filling a document by hand.

### Recommended sequence on a new project

A dependency chain, not a schedule: each step references the ones above it. [INSTRUCTIONS.md §5](./INSTRUCTIONS.md#5-the-build-sequence--checklist) carries the same sequence as a tickable checklist.

1. **Fill `project_info.md`**: a starting point, not a finished document; `TBC` is a perfectly good value. It is the fastest orientation any later reader or agent gets.
2. **Load the project documents.** Drop the executed contracts, OEM specs, interconnection agreement, and SCADA drawing sets into their `Project_Documentation/` domain folders.
3. **Review each one, one document at a time.** Run the `project-document-review` skill per document to produce its companions beside the source file: a `summary.md` (key provisions and requirements, cited to clause), a **`definitions.md`** (every term *that document* defines, in its own words), and a `guarantees.md` wherever it carries guarantees, warranties, or LDs. Suggested order: design basis, offtake agreement, **battery supply agreement** (it defines the capacities the service agreement's formulas reference), OEM service agreement, interconnection agreement, O&M agreement(s), EPC contract, then the SCADA sets and OEM equipment specs. This is the step that gives every session below its context: they stop being blank-page interviews and become reviews of what the contracts already say.
4. **Definitions roll-up.** Run the `definitions-taxonomy` skill once every document has been reviewed: it reads all the per-document `definitions.md` extractions plus the summaries and consolidates them into the master glossary in `Definitions(DEF)/`, promoting the terms that travel beyond their own document, flagging every word two contracts define differently, and linking each entry back to the extraction it came from. The taxonomy session (canonical names, organization codes, ID schemes, in `Ontology(ONT)/taxonomy.md`) follows in the same sitting via the `ontology` skill. **Do this before anything below**, so every document that follows is written in one settled vocabulary, and re-run the roll-up whenever a later document review adds or changes a definition.
5. **Contact Register**: the named people and organizations behind the parties. No skill; fill the template directly. It can precede the map because the parties come from the contracts, and it is one of only two documents carrying no *Source EIM version* stamp.
6. **Entity Interaction Map.** Run the `eim-review-build` skill for the kickoff Q&A that turns the template into a project-specific map plus a list of open questions. **Everything below derives from it** and references its node IDs.

   > **Steps 3 and 6 can swap.** If someone in the room holds the whole picture (every entity, contract, and data flow) the map can be built from the interview alone, and the document review then becomes the verification pass that corrects it. Documents-first is slower to start but yields a map already grounded in the executed terms; interview-first is faster and only as good as memory.

7. **Performance Guarantee Matrix**: measured-performance guarantees, their calculation methodologies, and the data that delivers or shadows each one. Leads this phase: it is what the measurement chain below consumes.
8. **Data Interface Register**: the telemetry and interface inventory; depends on the SCADA sets and OEM specs from step 3.
9. **Groundwork Ontology** (`Ontology(ONT)/`): the segment taxonomy, measurement vocabulary, and canonical data models the Metrics Tree's formulas and the register's signal rows are written in (Definitions(DEF) is the prose authority, ONT the identifier authority). Seeded from the taxonomy session at step 4, then **filled ratchet-wise via the metrics fill loop**: each metric built in step 10 ratifies its terms here first, so steps 9 and 10 advance together. Its platform projection is the spec a data-platform agent builds dimension data and canonical tables from.
10. **Metrics Tree** (`Metrics_Tree(MT)/`): build the tree first, then the per-segment metric registry, then one calculation sheet per owner metric being built (in calculations/); depends on the Data Interface Register + Performance Guarantee Matrix, written in the ontology's ratified terms.
11. **Warranty Obligation Matrix**: defect-style warranties, claim paths, and owner-side compliance conditions; needs the EPC and OEM reviews.
12. **RACI Matrix**: who is responsible/accountable for each event and process, plus the escalation & communication sections (who calls whom, severity levels, notification deadlines; they also build on the Contact Register).
13. **Data products** (in `Data_Product(DP)/`): the recurring deliverables, once the documents above give them defensible numbers. Build them in dependency order: **Outage Tracker** first (the outage/derate event ledger every availability number reads; depends on the Performance Guarantee Matrix + the Metrics Tree), then the **Daily Performance Report**, the **Monthly Performance Report** which assembles everything, any **counterparty data exchange** the offtake agreement obliges, and **Settlement Reconciliation** (depends on the Data Interface Register). New products are scoped with the `data-product` skill.

The other two output types are **trigger-driven rather than sequenced**, which is why they carry no step numbers. **Work products** (`Work_Product(WP)/`) are the one-off studies and reviews the project should run: a reliability study, a spare parts review, a telemetry readiness assessment, a performance test review, an RCA after a significant event; the category index carries the recurring catalogue, and the telemetry readiness assessment is usually the first one worth running (it gates every metric downstream). **SOPs** (`Standard_Operating_Procedures(SOP)/`) mostly fall *out* of the other work: a study concluding "the operator should do X when Y" hands off an SOP, the RACI's escalation paths each need a procedure behind them, and the warranty matrix's owner-side conditions are only met if somebody follows one.

Alongside these, the **tree** inside Metrics Tree is a cross-cutting view rather than a sequenced step: it organizes every metric into limbs (the generic baseline: Commercial Value and Asset Health) and feeds dashboards and the Monthly Performance Report.

Most populated documents carry an `EIM_VERSION` header: the registers and procedures (all except the Contact Register and the Monthly Report) trace back to a specific EIM version. When the EIM changes, search for that version to find the documents that need review.

## Resources

All resources below are **templates**. The workflow: **clone this entire repository per project**, then edit each template **in place** with the project-specific content: the project clone carries exactly one file per document (the populated template), while the pristine bases always live here in the master repo. Versioning happens inside each document (version header), not in filenames.

### Layer 1: Project inputs

| Resource | Skill | Description |
|:---|:---|:---|
| [Project Information](./project_info.md) | — | Root scaffold for the key facts of the project a repo clone is dedicated to: capacity, equipment, commercial structure, counterparties, milestones, open items. |
| [Project Documentation](./Project_Documentation/index.md) | [`project-document-review`](./.agents/skills/project-document-review/SKILL.md) | Domain folder structure for client-provided source documents (offtake, battery supply, service agreement, EPC, interconnection, O&M, OEM, SCADA, design). Each domain carries an `instructions.md` review method (four worked archetypes ship) and each reviewed document gets its companion set (`summary`, `definitions`, `guarantees`, and the domain's others) beside the original file. |

### Layer 2: Toolkit documents

| Resource | Skill | Status | Description |
|:---|:---|:---|:---|
| [Entity Interaction Map](./Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) | [`eim-review-build`](./.agents/skills/eim-review-build/SKILL.md) | Template | Topology template mapping the people, companies, systems, and data flows surrounding a BESS asset. The anchor for everything else. |
| [Contact Register](./Contact_Register%28CR%29/contact-register.md) | — | Template | Named contacts and organizations behind the EIM entities, with roles and reachability. |
| [Data Interface Register](./Data_Interface_Register%28DIR%29/data-interface-register.md) | [`data-interface-register`](./.agents/skills/data-interface-register/SKILL.md) | Template | Catalogue of data systems, telemetry chains, protocols, and integration points feeding the platform. |
| [RACI Matrix](./RACI_Matrix%28RACI%29/raci-matrix.md) | [`raci-matrix`](./.agents/skills/raci-matrix/SKILL.md) | Template | Responsible/Accountable/Consulted/Informed assignments for operational events and processes, plus the escalation & communication plan (severity levels, who-calls-whom chains, notification deadlines) built on the accountable parties. |
| [Performance Guarantee Matrix](./Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) | [`performance-guarantee-matrix`](./.agents/skills/performance-guarantee-matrix/SKILL.md) | Template | Inventory of measured-performance guarantees with their exact calculation methodologies, exclusion asymmetries, and shadow-calculation requirements. |
| [Warranty Obligation Matrix](./Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) | [`warranty-obligation-matrix`](./.agents/skills/warranty-obligation-matrix/SKILL.md) | Template | Inventory of defect-style warranties, claim paths, and the owner-side conditions that keep coverage valid. |
| [Metrics Tree](./Metrics_Tree%28MT%29/metrics-tree.md) | [`metrics-tree`](./.agents/skills/metrics-tree/SKILL.md) | Template | The tree (decomposition of the headline value, grouped by governing agreement) plus the per-segment metric registry (PG-xxx contractual rows referencing the PGM; MT_ owner metrics with calculation sheets in calculations/). |
| [Definitions](./Definitions%28DEF%29/index.md) | [`definitions-taxonomy`](./.agents/skills/definitions-taxonomy/SKILL.md) | Template | Global glossary: every acronym/term/organization with its project meaning, ambiguous terms flagged. The naming authority (taxonomy: codes, ID schemes, segment grammar) lives in Ontology(ONT)/taxonomy.md. |
| [Groundwork Ontology](./Ontology%28ONT%29/index.md) | [`ontology`](./.agents/skills/ontology/SKILL.md) | Template | The semantic foundation: segment taxonomy, measurement vocabulary (one name/unit/definition per quantity), and canonical data models per segment type, plus the platform-projection spec an agent uses to build dimension data and canonical tables. DT is the prose authority; ONT is the identifier authority. |

### Layer 3: Outputs

Three kinds, distinguished by cadence and audience: **data products** recur on a fixed cadence and are generated by a pipeline; **work products** are point-in-time studies ending in findings; **SOPs** stand until revised and are executed by someone else. Each item gets its own subfolder; new data products are scoped with the [`data-product`](./.agents/skills/data-product/SKILL.md) skill.

| Resource | Skill | Status | Description |
|:---|:---|:---|:---|
| [Data Products](./Data_Product%28DP%29/index.md) | [`data-product`](./.agents/skills/data-product/SKILL.md) | Category | Catalogue of recurring deliverables generated from the toolkit's documents and plant data: reports, dashboards, evidence packs, counterparty exchanges, exports. |
| [Outage Tracker](./Data_Product%28DP%29/Outage_Tracker/outage-tracker.md) | [`outage-tracker`](./.agents/skills/outage-tracker/SKILL.md) | Template | GADS-aligned outage/derate event taxonomy for BESS: one authoritative event record, NERC-export-ready. The taxonomy is a standing reference; the recurring output is the event ledger every availability calculation reads. |
| [Settlement Reconciliation](./Data_Product%28DP%29/Settlement_Reconciliation/settlement-reconciliation.md) | [`settlement-reconciliation`](./.agents/skills/settlement-reconciliation/SKILL.md) | Template | Shadow-settlement procedure verifying ISO statements against project meter data and awards; the recurring output is the monthly reconciliation. |
| [Monthly Performance Report](./Data_Product%28DP%29/Monthly_Performance_Report/monthly-performance-report.md) | [`monthly-performance-report`](./.agents/skills/monthly-performance-report/SKILL.md) | Template | The assembled monthly/quarterly reporting pack for owner, lender, and IE (the first data product). |
| [Daily Performance Report](./Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) | [`data-product`](./.agents/skills/data-product/SKILL.md) | Template | Daily availability under each measurement definition (equipment, power delivery, operational, combined) with excused and depth-weighted variants, energy and efficiency, data quality, the offtaker and service-provider contractual views, and a reproducibility settings block. Specification with worked panel screenshots and a build architecture. |
| [Performance Engineering Management](./Data_Product%28DP%29/Performance_Engineering_Management/performance-engineering-management.md) | — | Template | The management layer of the operation: daily review checklist, observation → action → case escalation, the outage event log feeding contract exclusions, the engineering change log, and a knowledge base with document register: register schemas, linking model, and cadences for a collaboration platform (Notion recommended). |
| [Dashboards](./Data_Product%28DP%29/Dashboards/dashboards.md) | — | Template | Monitoring and diagnostic dashboards: design rules (metric parity, diagnostic-not-contractual, hierarchy drill-down, linkable panels, audience tiers), the four-tier worked suite (fleet, site, device, analytics) with screenshots from a live deployment, and the per-project dashboard register. Grafana. |
| [Grid Telemetry Mapping](./Data_Product%28DP%29/Grid_Telemetry_Mapping/grid-telemetry-map.md) | [`data-product`](./.agents/skills/data-product/SKILL.md) | Template | The grid telemetry points (BESS ↔ grid operator) from the BESS SCADA's point of view: telemetry out, setpoints/commands in, with each point's source, the performance engineer's monitoring tag, and the grid-side address. |
| [State of Energy Mapping](./Data_Product%28DP%29/State_of_Energy_Mapping/state-of-energy-mapping.md) | [`data-product`](./.agents/skills/data-product/SKILL.md) | Template | The remap from installed state of energy (OEM fleet) to contractual state of energy (the offtaker's 0-to-contracted range): buffers, the remap function block and its accounting gains, computation owner, and the layered counting/steering/recalibration algorithm. |
| [Work Products](./Work_Product%28WP%29/index.md) | — | Category | Engineering studies and reviews produced for the project: reliability study, spare parts review, failover/redundancy test study, telemetry readiness assessment, performance test review, augmentation review, LD exposure review, RCA. Point-in-time analyses ending in findings, as opposed to the recurring deliverables in Data Products. |
| [Standard Operating Procedures](./Standard_Operating_Procedures%28SOP%29/index.md) | — | Category | Step-by-step procedures issued to operating parties and counterparties: controlled documents with owner, revision, distribution list, and acknowledgement tracking. A study concluding "someone should do X when Y" hands its procedure off here. |

## Using with AI agents

Optional: every document here is plain Markdown and can be read and edited by hand. Agents make the facilitated sessions and document reviews much faster.

[`AGENTS.md`](./AGENTS.md) is the entry point for any AI coding agent, and the facilitated-session skills live in [`.agents/skills/`](./.agents/skills/); that directory is the single source of truth for skills regardless of which agent you use. Local agent config (`CLAUDE.md`, `.claude/`, `.codex/`, etc.) can be set up per user without touching the shared repo.

Alongside the document-building skills above, three **maintenance skills** operate on the repo itself rather than on a single document: [`drafting-quality-control`](./.agents/skills/drafting-quality-control/SKILL.md) (house drafting conventions and the pre-commit prose pass), [`genericity-audit`](./.agents/skills/genericity-audit/SKILL.md) (scan for client, project, and vendor identifiers before anything is published or shared), and [`template-sync`](./.agents/skills/template-sync/SKILL.md) (port improvements between a project clone and this template).

Agents answer questions in a fixed retrieval order (see the *Answering questions* section of [`AGENTS.md`](./AGENTS.md)): a local knowledge-graph index if you built one, then the Markdown companions, then the source PDFs behind them, and only then the web. Web search is last and is barred for project facts. Building the optional graph index is covered in [INSTRUCTIONS.md](./INSTRUCTIONS.md#8-working-with-an-ai-agent).

### Claude Code

1. Recent versions of Claude Code read `AGENTS.md` automatically. If yours doesn't, create a local `CLAUDE.md` at the repo root containing: `Read AGENTS.md — it is the entry point for this repo.`
2. To make the skills natively discoverable (so Claude Code offers them automatically), symlink them into place:

   ```sh
   mkdir -p .claude && ln -s ../.agents/skills .claude/skills
   ```

   Don't copy skills into `.claude/`; edit them only in `.agents/skills/`.
3. Then just ask, e.g. *"build the EIM for [project]"*, and the matching skill runs the facilitated session.

### Codex

Codex reads `AGENTS.md` out of the box, no setup needed. `AGENTS.md` points it to the skills in `.agents/skills/`; ask for a document (e.g. *"build the Data Interface Register"*) and it will follow the matching `SKILL.md`.

## Contributing

This is a living collection. Resources are added and revised as the work evolves. Project-specific work happens in per-project clones of this repo; structural, project-agnostic improvements made in a clone should be ported back here so the pattern stays reusable.

## License

Maintained by [Librenergy](https://librenergy.com). Licensed under the Apache License, Version 2.0. See [LICENSE](./LICENSE) for details.
