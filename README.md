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

## How to use it

Everything in this toolkit anchors on the project **Entity Interaction Map (EIM)** — a Mermaid diagram of every entity (companies, contracts, assets, data systems) and interaction on a BESS project. The satellite documents all derive from it and reference its node IDs.

Each document is built through a facilitated Q&A session driven by a matching skill in `.agents/skills/`. Ask the agent to "build the EIM for [project]" (or any satellite document) and it will run the session, populate the template in place, bumping the version in its header.

### Recommended sequence on a new project

**Start with the Entity Interaction Map.** Run the `eim-review-build` skill for a kickoff Q&A that turns the EIM template into a project-specific map plus a list of open questions. Every document below depends on it. Then build the documents in this order:

1. **Contact Register** — the named people and organizations behind the EIM entities; needed by the escalation plan.
2. **Data Interface Register** — first of the foundations; the telemetry and interface inventory.
3. **Performance Guarantee Matrix** — second foundation; measured-performance guarantees, their calculation methodologies, and the data that shadows them.
4. **Warranty Obligation Matrix** — defect-style warranties, claim paths, and owner-side compliance conditions.
5. **Metrics Tree, then Metrics & KPIs** (in `Metrics_Tree(MT)/`) — build the tree first, then the metric definitions and KPI targets; depends on the Data Interface Register + Performance Guarantee Matrix.
6. **RACI Matrix** — who is responsible/accountable for each event and process.
7. **Escalation & Communication Plan** — who calls whom; builds on the RACI.
8. **BESS GADS** — the GADS-aligned outage/derate event accounting; depends on the Performance Guarantee Matrix + Metrics & KPIs.
9. **Settlement Reconciliation** — depends on the Data Interface Register.
10. **Monthly Performance Report** (a data product, in `Data_Product(DP)/`) — assembles everything above.

Alongside these, the **Metrics Tree** is a cross-cutting view rather than a sequenced step: once the EIM and Metrics & KPIs exist, it organizes every metric into limbs (the generic baseline: Commercial Value and Asset Health) and feeds dashboards and the Monthly Performance Report.

Most populated documents carry an `EIM_VERSION` header — the registers and procedures (all except the Contact Register and the Monthly Report) trace back to a specific EIM version. When the EIM changes, search for that version to find the documents that need review.

## Resources

All resources below are **templates**. The workflow: **clone this entire repository per project**, then edit each template **in place** with the project-specific content — the project clone carries exactly one file per document (the populated template), while the pristine bases always live here in the master repo. Versioning happens inside each document (version header + change log), not in filenames.

| Resource | Skill | Status | Description |
|:---|:---|:---|:---|
| [Entity Interaction Map](./Entity_Interaction_Map%28EIM%29/Entity_Interaction_Map%28EIM%29.md) | [`eim-review-build`](./.agents/skills/eim-review-build/SKILL.md) | Template | Topology template mapping the people, companies, systems, and data flows surrounding a BESS asset. The anchor for everything else. |
| [Contact Register](./Contact_Register%28CR%29/contact-register.md) | — | Template | Named contacts and organizations behind the EIM entities, with roles and reachability. |
| [Data Interface Register](./Data_Interface_Register%28DIR%29/data-interface-register.md) | [`data-interface-register`](./.agents/skills/data-interface-register/SKILL.md) | Template | Catalogue of data systems, telemetry chains, protocols, and integration points feeding the platform. |
| [RACI Matrix](./RACI_Matrix%28RACI%29/raci-matrix.md) | [`raci-matrix`](./.agents/skills/raci-matrix/SKILL.md) | Template | Responsible/Accountable/Consulted/Informed assignments for operational events and processes. |
| [Performance Guarantee Matrix](./Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) | [`performance-guarantee-matrix`](./.agents/skills/performance-guarantee-matrix/SKILL.md) | Template | Inventory of measured-performance guarantees with their exact calculation methodologies, exclusion asymmetries, and shadow-calculation requirements. |
| [Warranty Obligation Matrix](./Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) | [`warranty-obligation-matrix`](./.agents/skills/warranty-obligation-matrix/SKILL.md) | Template | Inventory of defect-style warranties, claim paths, and the owner-side conditions that keep coverage valid. |
| [Metrics & KPIs](./Metrics_Tree%28MT%29/metrics-and-kpis.md) | [`metrics-tree`](./.agents/skills/metrics-tree/SKILL.md) | Template | Metric definitions (the authoritative calculations) and the KPI target set — lives beside the metrics tree. |
| [Escalation & Communication Plan](./Escalation_Communication_Plan%28ECP%29/escalation-communication-plan.md) | [`escalation-plan`](./.agents/skills/escalation-plan/SKILL.md) | Template | Who-calls-whom, severity levels, and notification deadlines for plant events. |
| [BESS GADS](./BESS_GADS%28GADS%29/bess-gads.md) | [`bess-gads`](./.agents/skills/bess-gads/SKILL.md) | Template | GADS-aligned outage/derate event taxonomy for BESS — one authoritative event record, NERC-export-ready. |
| [Data Products](./Data_Product%28DP%29/index.md) | [`data-product`](./.agents/skills/data-product/SKILL.md) | Category | Catalogue of recurring deliverables generated from the toolkit's documents and plant data — reports, dashboards, evidence packs, exports; each product gets its own subfolder. |
| [Monthly Performance Report](./Data_Product%28DP%29/Monthly_Performance_Report%28MPR%29/monthly-performance-report.md) | [`monthly-performance-report`](./.agents/skills/monthly-performance-report/SKILL.md) | Template | The assembled monthly/quarterly reporting pack for owner, lender, and IE — the first data product. |
| [Settlement Reconciliation](./Settlement_Reconciliation%28SR%29/settlement-reconciliation.md) | [`settlement-reconciliation`](./.agents/skills/settlement-reconciliation/SKILL.md) | Template | Shadow-settlement procedure verifying ISO statements against project meter data and awards. |
| [Metrics Tree](./Metrics_Tree%28MT%29/metrics-tree.md) | [`metrics-tree`](./.agents/skills/metrics-tree/SKILL.md) | Template | Decomposition of the headline value through LDs/adjustments and drivers down to metrics, signals, and data sources. |
| [Definitions & Taxonomy](./Definitions_Taxonomy%28DT%29/index.md) | [`definitions-taxonomy`](./.agents/skills/definitions-taxonomy/SKILL.md) | Template | Global glossary (every acronym/term/organization with its project meaning, ambiguous terms flagged) plus naming authority (organization codes, canonical terms, ID-prefix conventions). |
| [Project Information](./project_info.md) | — | Template | Root scaffold for the key facts of the project a repo clone is dedicated to: capacity, equipment, commercial structure, counterparties, milestones, open items. |

## Using with AI agents

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
