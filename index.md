# Index

## Start here

* [AGENTS.md](/AGENTS.md) - Entry point for AI agents: what the toolkit is, how documents get built, and the OKF conventions.
* [README.md](/README.md) - Full index with the recommended build sequence, skill mapping, and agent setup instructions.

## Templates

Everything anchors on the Entity Interaction Map; the satellite documents derive from it. Build order: EIM first, then the recommended sequence in the README.

* [Entity Interaction Map (EIM)](/Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) - Topology of people, companies, systems, and data flows around a BESS asset; the anchor for everything else.
* [Contact Register](/Contact_Register%28CR%29/contact-register.md) - Named contacts and organizations behind the EIM entities.
* [Data Interface Register (DIR)](/Data_Interface_Register%28DIR%29/data-interface-register.md) - Catalogue of data systems, telemetry chains, protocols, and integration points.
* [RACI Matrix](/RACI_Matrix%28RACI%29/raci-matrix.md) - Responsible/Accountable/Consulted/Informed assignments for operational events.
* [Performance Guarantee Matrix (PGM)](/Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) - Measured-performance guarantees with their calculation methodologies, exclusion asymmetries, and shadow-calculation requirements.
* [Warranty Obligation Matrix](/Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) - Defect-style warranties, claim paths, and owner-side conditions that keep coverage valid.
* [Escalation & Communication Plan](/Escalation_Communication_Plan%28ECP%29/escalation-communication-plan.md) - Who-calls-whom, severity levels, and notification deadlines.
* [BESS GADS](/BESS_GADS%28GADS%29/bess-gads.md) - GADS-aligned outage/derate event taxonomy — one authoritative event record feeding every contractual availability calculation.
* [Monthly Performance Report](/Data_Product%28DP%29/Monthly_Performance_Report/monthly-performance-report.md) - Assembled monthly/quarterly reporting pack for owner, lender, and IE.
* [Daily Performance Report](/Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) - Daily limit tracking against contract usage limits and guarantees, with the design rules for a daily counterparty data exchange.
* [Settlement Reconciliation](/Settlement_Reconciliation%28SR%29/settlement-reconciliation.md) - Shadow-settlement procedure verifying ISO statements against meter data and awards.
* [Metrics Tree & KPIs](/Metrics_Tree%28MT%29/metrics-tree.md) - One document: the tree (Commercial Value and Asset Health limbs), the authoritative metric definitions, and the KPI target set.
* [Definitions & Taxonomy](/Definitions_Taxonomy%28DT%29/index.md) - Global glossary of acronyms and terms plus naming/coding conventions (organization codes, ID prefixes) for generated documents.
* [Project Information](/project_info.md) - Blank scaffold for key project facts: capacity, equipment, commercial structure, counterparties, milestones, and open items.

## Project scaffolding

* [Project Documentation](/Project_Documentation/index.md) - Domain folder structure (PPA, LTSA, EPC, GRID, O&M, OEM, SCADA, EMS, Design, Asset Management, Performance Engineering) for client-provided source documents, ready for the `project-document-review` skill.

## Skills

* [.agents/skills/](/.agents/skills/) - Facilitated Q&A sessions that populate each template; one skill per document, indexed in the README's Resources table.
