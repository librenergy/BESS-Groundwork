---
type: Index
title: BESS Performance Engineering
description: "Index of templates, skills, and reference materials for performance engineering and asset management on BESS assets."
---

# BESS Performance Engineering

A collection of templates, reference materials, and structured resources for performance engineering on Battery Energy Storage System (BESS) assets.

Asset like Charts also available on Librenergy's Lucidechart (user must be loged in with free account): https://lucid.app/folder/invitations/accept?invitationId=inv_9b8029e4-53c5-449d-9252-888888381a2e



## Purpose

This repository is a shared workspace for the tools and context needed to understand, measure, and improve BESS fleet performance. It is intended to support:

- **Data architecture** — Topology mapping, signal inventories, and data flow documentation.
- **Metrics design** — Grounded definitions that trace back to real sources, sinks, and contracts.
- **Workflow context** — End-to-end chains from alarm to action, including human and system handoffs.
- **AI agent context** — Structured topology and ownership boundaries for LLM-based analysis and automation.
- **Contract review** — Mapping LTSA scopes, SLA boundaries, and warranty obligations to actual data flows and system interfaces.

## How to use it

Everything in this toolkit anchors on the project **Entity Interaction Map (EIM)** — a Mermaid diagram of every entity (companies, contracts, assets, data systems) and interaction on a BESS project. The nine satellite documents all derive from it and reference its node IDs.

Each document is built through a facilitated Q&A session driven by a matching skill in `.agents/skills/`. Ask the agent to "build the EIM for [project]" (or any satellite document) and it will run the session, populate the template, and save a versioned, project-specific copy alongside the base.

### Recommended sequence on a new project

**Start with the Entity Interaction Map.** Run the `eim-review-build` skill for a kickoff Q&A that turns the EIM template into a project-specific map plus a list of open questions. Every document below depends on it. Then build the nine documents in this order:

1. **Contact Register (00)** — the named people and organizations behind the EIM entities; needed by the escalation plan.
2. **Data Interface Register (01)** — first of the two foundations; the telemetry and interface inventory.
3. **Warranty Obligation Matrix (03)** — second foundation; guaranteed metrics and the data that proves them.
4. **KPI Definitions (04)** — depends on 01 + 03.
5. **RACI Matrix (02)** — who is responsible/accountable for each event and process.
6. **Escalation & Communication Plan (05)** — who calls whom; builds on the RACI.
7. **Outage & Availability Accounting (06)** — depends on 03 + 04.
8. **Settlement Reconciliation (08)** — depends on 01.
9. **Monthly Performance Report (07)** — assembles everything above.

Most populated documents carry an `EIM_VERSION` header — the numbered registers and procedures (01–08, except the Monthly Report) trace back to a specific EIM version. When the EIM changes, search for that version to find the documents that need review.

## Resources

All resources below are **templates**. They are meant to be copied, not edited in place: clone this repository and create a project-specific version of each document for your asset, keeping the base template intact for reuse.

| # | Resource | Skill | Status | Description |
|:---|:---|:---|:---|:---|
| — | [Entity Interaction Map](./Entity_Interaction_Map/Entity_Interaction_Map.md) | [`eim-review-build`](./.agents/skills/eim-review-build/SKILL.md) | Template | Topology template mapping the people, companies, systems, and data flows surrounding a BESS asset. The anchor for everything else. |
| 00 | [Contact Register](./Contact_Register/00-contact-register.md) | — | Template | Named contacts and organizations behind the EIM entities, with roles and reachability. |
| 01 | [Data Interface Register](./Data_Interface_Register/01-data-interface-register.md) | [`data-interface-register`](./.agents/skills/data-interface-register/SKILL.md) | Template | Catalogue of data systems, telemetry chains, protocols, and integration points feeding the platform. |
| 02 | [RACI Matrix](./RACI_Matrix/02-raci-matrix.md) | [`raci-matrix`](./.agents/skills/raci-matrix/SKILL.md) | Template | Responsible/Accountable/Consulted/Informed assignments for operational events and processes. |
| 03 | [Warranty Obligation Matrix](./Warranty_Obligation_Matrix/03-warranty-obligation-matrix.md) | [`warranty-obligation-matrix`](./.agents/skills/warranty-obligation-matrix/SKILL.md) | Template | Inventory of performance guarantees and warranties mapped to the data sources that prove them. |
| 04 | [KPI Definitions](./KPI_Definitions/04-kpi-definitions.md) | [`kpi-definitions`](./.agents/skills/kpi-definitions/SKILL.md) | Template | Authoritative metric definitions and measurement basis, traced to sources and contracts. |
| 05 | [Escalation & Communication Plan](./Escalation_Communication_Plan/05-escalation-communication-plan.md) | [`escalation-plan`](./.agents/skills/escalation-plan/SKILL.md) | Template | Who-calls-whom, severity levels, and notification deadlines for plant events. |
| 06 | [Outage & Availability Procedure](./Outage_Availability_Procedure/06-outage-availability-procedure.md) | [`outage-availability-accounting`](./.agents/skills/outage-availability-accounting/SKILL.md) | Template | Outage categorization and availability accounting reconciled across LTSA, ISO, and platform. |
| 07 | [Monthly Performance Report](./Monthly_Performance_Report/07-monthly-performance-report.md) | [`monthly-performance-report`](./.agents/skills/monthly-performance-report/SKILL.md) | Template | The assembled monthly/quarterly reporting pack for owner, lender, and IE. |
| 08 | [Settlement Reconciliation](./Settlement_Reconciliation/08-settlement-reconciliation.md) | [`settlement-reconciliation`](./.agents/skills/settlement-reconciliation/SKILL.md) | Template | Shadow-settlement procedure verifying ISO statements against project meter data and awards. |

## Contributing

This is a living collection. Resources are added and revised as the work evolves. If you extend a template for a specific asset, site, or use case, commit it alongside the base version so the pattern is reusable.

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](./LICENSE) for details.
