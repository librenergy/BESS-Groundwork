---
type: Index
title: BESS Performance Engineering
description: "Index of templates, skills, and reference materials for performance engineering and asset management on BESS assets."
---

# BESS Performance Engineering

A collection of templates, reference materials, and structured resources for performance engineering on Battery Energy Storage System (BESS) assets. BESS-first by design, but the method — an entity map, satellite documents, a metrics layer, and data products — is asset-agnostic and transfers to PV, wind, and hybrid plants.



## Purpose

This repository is a shared workspace for the tools and context needed to understand, measure, and improve BESS fleet performance. It is intended to support:

- **Data architecture** — Topology mapping, signal inventories, and data flow documentation.
- **Metrics design** — Grounded definitions that trace back to real sources, sinks, and contracts.
- **Workflow context** — End-to-end chains from alarm to action, including human and system handoffs.
- **AI agent context** — Structured topology and ownership boundaries for LLM-based analysis and automation.
- **Contract review** — Mapping LTSA scopes, SLA boundaries, and warranty obligations to actual data flows and system interfaces.

## How the repository is organised

Three layers, each built from the one below it:

| Layer | What it holds | Where |
|:---|:---|:---|
| **1 — Project inputs** | The raw facts: key project data, plus every client-provided contract, spec, and drawing set, each reviewed into Markdown companions (`summary.md`, `definitions.md`, `guarantees.md`) beside the original file. | [`project_info.md`](./project_info.md), [`Project_Documentation/`](./Project_Documentation/index.md) |
| **2 — Toolkit documents** | The engineering layer built *from* the inputs: the entity map, the registers, the guarantee and warranty matrices, the metrics layer, the operating procedures. One folder per document. | The document folders below |
| **3 — Data products** | The recurring deliverables generated from layers 1 and 2: reports, counterparty exchanges, exports. One subfolder per product. | [`Data_Product(DP)/`](./Data_Product%28DP%29/index.md) |

Every folder follows the same shape: the document itself (established fact only), an `index.md` (contents plus status), a `log.md` (change history), and a `todo.md` (open items). Draft findings and analysis live in a `notes.md` where one is needed, so the formal documents stay clean.

## How to use it

**[INSTRUCTIONS.md](./INSTRUCTIONS.md) is the working guide** — how to stand up a project clone, find an answer, add a source document, run a build session, and follow the conventions. This README is the index and the build sequence.

Everything in layer 2 anchors on the project **Entity Interaction Map (EIM)** — a Mermaid diagram of every entity (companies, contracts, assets, data systems) and interaction on a BESS project. The satellite documents all derive from it and reference its node IDs.

Each document is built through a facilitated Q&A session driven by a matching skill in `.agents/skills/`. Ask the agent to "build the EIM for [project]" (or any satellite document) and it will run the session, populate the template in place, bumping the version in its header. The skills are readable prose — they also work as interview checklists if you are filling a document by hand.

### Recommended sequence on a new project

A dependency chain, not a schedule — each step references the ones above it. [INSTRUCTIONS.md §5](./INSTRUCTIONS.md#5-the-build-sequence--checklist) carries the same sequence as a tickable checklist.

1. **Fill `project_info.md`** — a starting point, not a finished document; `TBC` is a perfectly good value. It is the fastest orientation any later reader or agent gets.
2. **Load the project documents.** Drop the executed contracts, OEM specs, interconnection agreement, and SCADA drawing sets into their `Project_Documentation/` domain folders.
3. **Review each one, one document at a time.** Run the `project-document-review` skill per document to produce its companions beside the source file — a `summary.md` (key provisions and requirements, cited to clause), a **`definitions.md`** (every term *that document* defines, in its own words), and a `guarantees.md` wherever it carries guarantees, warranties, or LDs. Suggested order — design basis, offtake agreement, **battery supply agreement** (it defines the capacities the service agreement's formulas reference), OEM service agreement, interconnection agreement, O&M agreement(s), EPC contract, then the SCADA sets and OEM equipment specs. This is the step that gives every session below its context: they stop being blank-page interviews and become reviews of what the contracts already say.
4. **Definitions & Taxonomy** — the roll-up. Run the `definitions-taxonomy` skill once every document has been reviewed: it reads all the per-document `definitions.md` extractions plus the summaries and consolidates them into the master glossary in `Definitions_Taxonomy(DT)/`, promoting the terms that travel beyond their own document, flagging every word two contracts define differently, and linking each entry back to the extraction it came from. The taxonomy session (canonical names, organization codes, ID prefixes) follows in the same sitting. **Do this before anything below**, so every document that follows is written in one settled vocabulary — and re-run the roll-up whenever a later document review adds or changes a definition.
5. **Contact Register** — the named people and organizations behind the parties. No skill; fill the template directly. It can precede the map because the parties come from the contracts, and it is one of only two documents carrying no *Source EIM version* stamp.
6. **Entity Interaction Map.** Run the `eim-review-build` skill for the kickoff Q&A that turns the template into a project-specific map plus a list of open questions. **Everything below derives from it** and references its node IDs.

   > **Steps 3 and 6 can swap.** If someone in the room holds the whole picture — every entity, contract, and data flow — the map can be built from the interview alone, and the document review then becomes the verification pass that corrects it. Documents-first is slower to start but yields a map already grounded in the executed terms; interview-first is faster and only as good as memory.

7. **Warranty Obligation Matrix** — defect-style warranties, claim paths, and owner-side compliance conditions; needs the EPC and OEM reviews.
8. **Performance Guarantee Matrix** — measured-performance guarantees, their calculation methodologies, and the data that shadows them.
9. **RACI Matrix** — who is responsible/accountable for each event and process.
10. **Data Interface Register** — the telemetry and interface inventory; depends on the SCADA sets and OEM specs from step 3.
11. **Metrics Tree & KPIs** (one document in `Metrics_Tree(MT)/`) — build the tree first, then the metric definitions and KPI targets below it; depends on the Data Interface Register + Performance Guarantee Matrix.
12. **Escalation & Communication Plan** — who calls whom; builds on the RACI and the Contact Register.
13. **Data products** (in `Data_Product(DP)/`) — the recurring deliverables, once the documents above give them defensible numbers. Build them in dependency order: **BESS GADS** first (the outage/derate event ledger every availability number reads — depends on the Performance Guarantee Matrix + Metrics & KPIs), then the **Daily Performance Report**, the **Monthly Performance Report** which assembles everything, any **counterparty data exchange** the offtake agreement obliges, and **Settlement Reconciliation** (depends on the Data Interface Register). New products are scoped with the `data-product` skill.

Alongside these, the **tree** inside Metrics Tree & KPIs is a cross-cutting view rather than a sequenced step: it organizes every metric into limbs (the generic baseline: Commercial Value and Asset Health) and feeds dashboards and the Monthly Performance Report.

Most populated documents carry an `EIM_VERSION` header — the registers and procedures (all except the Contact Register and the Monthly Report) trace back to a specific EIM version. When the EIM changes, search for that version to find the documents that need review.

## Resources

All resources below are **templates**. The workflow: **clone this entire repository per project**, then edit each template **in place** with the project-specific content — the project clone carries exactly one file per document (the populated template), while the pristine bases always live here in the master repo. Versioning happens inside each document (version header), not in filenames.

### Layer 1 — Project inputs

| Resource | Skill | Description |
|:---|:---|:---|
| [Project Information](./project_info.md) | — | Root scaffold for the key facts of the project a repo clone is dedicated to: capacity, equipment, commercial structure, counterparties, milestones, open items. |
| [Project Documentation](./Project_Documentation/index.md) | [`project-document-review`](./.agents/skills/project-document-review/SKILL.md) | Domain folder structure for client-provided source documents (offtake, battery supply, service agreement, EPC, interconnection, O&M, OEM, SCADA, design). Each domain carries an `instructions.md` review method — four worked archetypes ship — and each reviewed document gets its companion set (`summary`, `definitions`, `guarantees`, and the domain's others) beside the original file. |

### Layer 2 — Toolkit documents

| Resource | Skill | Status | Description |
|:---|:---|:---|:---|
| [Entity Interaction Map](./Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) | [`eim-review-build`](./.agents/skills/eim-review-build/SKILL.md) | Template | Topology template mapping the people, companies, systems, and data flows surrounding a BESS asset. The anchor for everything else. |
| [Contact Register](./Contact_Register%28CR%29/contact-register.md) | — | Template | Named contacts and organizations behind the EIM entities, with roles and reachability. |
| [Data Interface Register](./Data_Interface_Register%28DIR%29/data-interface-register.md) | [`data-interface-register`](./.agents/skills/data-interface-register/SKILL.md) | Template | Catalogue of data systems, telemetry chains, protocols, and integration points feeding the platform. |
| [RACI Matrix](./RACI_Matrix%28RACI%29/raci-matrix.md) | [`raci-matrix`](./.agents/skills/raci-matrix/SKILL.md) | Template | Responsible/Accountable/Consulted/Informed assignments for operational events and processes. |
| [Performance Guarantee Matrix](./Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) | [`performance-guarantee-matrix`](./.agents/skills/performance-guarantee-matrix/SKILL.md) | Template | Inventory of measured-performance guarantees with their exact calculation methodologies, exclusion asymmetries, and shadow-calculation requirements. |
| [Warranty Obligation Matrix](./Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) | [`warranty-obligation-matrix`](./.agents/skills/warranty-obligation-matrix/SKILL.md) | Template | Inventory of defect-style warranties, claim paths, and the owner-side conditions that keep coverage valid. |
| [Escalation & Communication Plan](./Escalation_Communication_Plan%28ECP%29/escalation-communication-plan.md) | [`escalation-plan`](./.agents/skills/escalation-plan/SKILL.md) | Template | Who-calls-whom, severity levels, and notification deadlines for plant events. |
| [Metrics Tree & KPIs](./Metrics_Tree%28MT%29/metrics-tree.md) | [`metrics-tree`](./.agents/skills/metrics-tree/SKILL.md) | Template | One document: the metrics tree (decomposition of the headline value down to data sources), the authoritative metric definitions, and the KPI target set. |
| [Definitions & Taxonomy](./Definitions_Taxonomy%28DT%29/index.md) | [`definitions-taxonomy`](./.agents/skills/definitions-taxonomy/SKILL.md) | Template | Global glossary (every acronym/term/organization with its project meaning, ambiguous terms flagged) plus naming authority (organization codes, canonical terms, ID-prefix conventions). |

### Layer 3 — Data products

Recurring deliverables generated from the documents above and plant data. Each product gets its own subfolder; new ones are scoped with the [`data-product`](./.agents/skills/data-product/SKILL.md) skill.

| Resource | Skill | Status | Description |
|:---|:---|:---|:---|
| [Data Products](./Data_Product%28DP%29/index.md) | [`data-product`](./.agents/skills/data-product/SKILL.md) | Category | Catalogue of recurring deliverables generated from the toolkit's documents and plant data — reports, dashboards, evidence packs, counterparty exchanges, exports. |
| [BESS GADS](./Data_Product%28DP%29/BESS_GADS/bess-gads.md) | [`bess-gads`](./.agents/skills/bess-gads/SKILL.md) | Template | GADS-aligned outage/derate event taxonomy for BESS — one authoritative event record, NERC-export-ready. The taxonomy is a standing reference; the recurring output is the event ledger every availability calculation reads. |
| [Settlement Reconciliation](./Data_Product%28DP%29/Settlement_Reconciliation/settlement-reconciliation.md) | [`settlement-reconciliation`](./.agents/skills/settlement-reconciliation/SKILL.md) | Template | Shadow-settlement procedure verifying ISO statements against project meter data and awards; the recurring output is the monthly reconciliation. |
| [Monthly Performance Report](./Data_Product%28DP%29/Monthly_Performance_Report/monthly-performance-report.md) | [`monthly-performance-report`](./.agents/skills/monthly-performance-report/SKILL.md) | Template | The assembled monthly/quarterly reporting pack for owner, lender, and IE — the first data product. |
| [Daily Performance Report](./Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) | [`data-product`](./.agents/skills/data-product/SKILL.md) | Template | Daily availability under each contractual definition (equipment, power, operational, combined) with excused and depth-weighted variants, energy and efficiency, data quality, commercial events, and a reproducibility parameter block; Markdown spec + HTML render skeleton. |

## Using with AI agents

Optional — every document here is plain Markdown and can be read and edited by hand. Agents make the facilitated sessions and document reviews much faster.

[`AGENTS.md`](./AGENTS.md) is the entry point for any AI coding agent, and the facilitated-session skills live in [`.agents/skills/`](./.agents/skills/) — that directory is the single source of truth for skills regardless of which agent you use. Local agent config (`CLAUDE.md`, `.claude/`, `.codex/`, etc.) can be set up per user without touching the shared repo.

### Claude Code

1. Recent versions of Claude Code read `AGENTS.md` automatically. If yours doesn't, create a local `CLAUDE.md` at the repo root containing: `Read AGENTS.md — it is the entry point for this repo.`
2. To make the skills natively discoverable (so Claude Code offers them automatically), symlink them into place:

   ```sh
   mkdir -p .claude && ln -s ../.agents/skills .claude/skills
   ```

   Don't copy skills into `.claude/` — edit them only in `.agents/skills/`.
3. Then just ask, e.g. *"build the EIM for [project]"*, and the matching skill runs the facilitated session.

### Codex

Codex reads `AGENTS.md` out of the box — no setup needed. `AGENTS.md` points it to the skills in `.agents/skills/`; ask for a document (e.g. *"build the Data Interface Register"*) and it will follow the matching `SKILL.md`.

## Contributing

This is a living collection. Resources are added and revised as the work evolves. Project-specific work happens in per-project clones of this repo; structural, project-agnostic improvements made in a clone should be ported back here so the pattern stays reusable.

## License

Maintained by [Librenergy](https://librenergy.com). Licensed under the Apache License, Version 2.0. See [LICENSE](./LICENSE) for details.
