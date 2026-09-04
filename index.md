# Index

## Start here

* [INSTRUCTIONS.md](/INSTRUCTIONS.md) - Working guide for the people using a project clone: how to find an answer, add a source document, run a build session, follow the conventions, and stand up a new clone.
* [README.md](/README.md) - Full index: the three-layer structure, the recommended build sequence, skill mapping, and agent setup instructions.
* [AGENTS.md](/AGENTS.md) - Entry point for AI agents: what the toolkit is, how documents get built, and the OKF conventions.
* [todo.md](/todo.md) - The maintainer's repo-wide working backlog; document-specific open items live in each folder's own `todo.md`.

## Templates

Everything anchors on the Entity Interaction Map; the satellite documents derive from it. Build order: EIM first, then the recommended sequence in the README.

* [Entity Interaction Map (EIM)](/Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) - Topology of people, companies, systems, and data flows around a BESS asset; the anchor for everything else.
* [Contact Register](/Contact_Register%28CR%29/contact-register.md) - Named contacts and organizations behind the EIM entities.
* [Data Interface Register (DIR)](/Data_Interface_Register%28DIR%29/data-interface-register.md) - Catalogue of data systems, telemetry chains, protocols, and integration points.
* [RACI Matrix](/RACI_Matrix%28RACI%29/raci-matrix.md) - Responsible/Accountable/Consulted/Informed assignments for operational events, plus the escalation & communication sections (who-calls-whom, severity levels, notification deadlines).
* [Performance Guarantee Matrix (PGM)](/Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) - Measured-performance guarantees with their calculation methodologies, exclusion asymmetries, and shadow-calculation requirements.
* [Calculation Sheets](/Performance_Guarantee_Matrix%28PGM%29/calculations/index.md) - One standalone sheet per guarantee mechanism, plus the shared placeholder schema for the per-sheet pseudo-SQL sections.
* [BESS Experts](/BESS_Experts/index.md) - Training and reference material behind the toolkit: the BESS performance-engineering video series, its slide decks, and where each part lands in the repo. Grows on its own clock; the seed of the bessexperts.com community.
* [Warranty Obligation Matrix](/Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) - Defect-style warranties, claim paths, and owner-side conditions that keep coverage valid.
* [Metrics Tree](/Metrics_Tree%28MT%29/metrics-tree.md) - One document: the tree (Commercial Value and Asset Health limbs), the authoritative metric definitions, and the KPI target set.
* [Definitions & Taxonomy](/Definitions%28DEF%29/index.md) - Global glossary of acronyms and terms plus naming/coding conventions (organization codes, ID prefixes) for generated documents.
* [Project Information](/project_info.md) - Blank scaffold for key project facts: capacity, equipment, commercial structure, counterparties, milestones, and open items.

## Data products

Recurring deliverables generated from the templates above and plant data; each product gets its own plain-named subfolder. Build order within the category: the event ledger and the reconciliation feed the reports.

* [Data Products (DP)](/Data_Product%28DP%29/index.md) - Parent folder for generated deliverables: reports, dashboards, counterparty exchanges, exports.
* [Outage Tracker](/Data_Product%28DP%29/Outage_Tracker/index.md) - GADS-aligned outage/derate event taxonomy: one authoritative event record feeding every contractual availability calculation.
* [Settlement Reconciliation](/Data_Product%28DP%29/Settlement_Reconciliation/index.md) - Shadow-settlement procedure verifying counterparty statements against meter data and awards.
* [Daily Performance Report](/Data_Product%28DP%29/Daily_Performance_Report/index.md) - The daily operational instrument: availability under each measurement definition, the offtaker and service-provider contractual views, and the build architecture behind an automated daily pack.
* [Monthly Performance Report](/Data_Product%28DP%29/Monthly_Performance_Report/monthly-performance-report.md) - Assembled monthly/quarterly reporting pack for owner, lender, and IE.
* [Performance Engineering Management](/Data_Product%28DP%29/Performance_Engineering_Management/index.md) - The management layer of the operation: register set (daily review checklists, observations, actions, cases, outage events, engineering changes, knowledge base), linking model, and cadences for running performance engineering on a collaboration platform.
* [Dashboards](/Data_Product%28DP%29/Dashboards/index.md) - Monitoring and diagnostic dashboards: design rules, the four-tier worked suite (fleet, site, device, analytics) with screenshots, and the per-project register tying each dashboard to its metric definitions and data sources.
* [Grid Telemetry Mapping](/Data_Product%28DP%29/Grid_Telemetry_Mapping/index.md) - The grid telemetry points (BESS ↔ grid operator) from the BESS SCADA's point of view: each point's source, the performance engineer's monitoring tag, and the grid-side address: how the owner watches an interface the utility side keeps unreadable.
* [State of Energy Mapping](/Data_Product%28DP%29/State_of_Energy_Mapping/index.md) - The remap from installed state of energy (OEM fleet) to contractual state of energy (the offtaker's 0-to-contracted range): buffers, the remap function block and its accounting gains, computation owner, and the layered counting/steering/recalibration algorithm.

## Work products

* [Work Products (WP)](/Work_Product%28WP%29/index.md) - Engineering studies and reviews the performance-engineering team produces: point-in-time analyses ending in findings; first worked template: the IT systems reliability study.

## Standard operating procedures

* [Standard Operating Procedures (SOP)](/Standard_Operating_Procedures%28SOP%29/index.md) - Step-by-step procedures issued to operating parties and counterparties: controlled documents with issuance and acknowledgement tracking; category scaffold with the recurring procedure catalogue.

## Project scaffolding

* [Project Documentation](/Project_Documentation/index.md) - Domain folder structure (PPA, BSA, LTSA, EPC, GRID, O&M, OEM, SCADA, EMS, Design, Asset Management, Performance Engineering) for client-provided source documents, each carrying an `instructions.md` review method; four worked archetypes ship (offtake, supply, service, OEM documentation).

## Skills

* [.agents/skills/](/.agents/skills/) - Facilitated Q&A sessions that populate each template; one skill per document, indexed in the README's Resources table.
