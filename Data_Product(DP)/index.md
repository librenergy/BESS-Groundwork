# Data Products

Deliverables generated *from* the toolkit's documents and data — reports, dashboards, exports. Each product gets its own subfolder, **plain-named** (no acronym suffix inside an acronym-suffixed parent). New products are scoped through the [`data-product` skill](../.agents/skills/data-product/SKILL.md); the Monthly Performance Report, the Outage Tracker, and Settlement Reconciliation have their own dedicated skills.

Roughly in dependency order — the event ledger and the reconciliation supply the numbers the reports assemble.

* [Outage Tracker](Outage_Tracker/index.md) - Commercial outage/derate event tracker with a GADS-aligned taxonomy. The taxonomy is a standing reference; the recurring output is the event ledger every availability calculation reads.
* [Settlement Reconciliation](Settlement_Reconciliation/index.md) - Shadow-settlement procedure verifying counterparty statements against meter data and awards; the recurring output is the monthly reconciliation.
* [Daily Performance Report](Daily_Performance_Report/index.md) - The operational counterpart to the monthly pack: availability under each measurement definition (equipment, power delivery, operational, combined) with excused and depth-weighted variants, energy and efficiency, data quality, the offtaker and service-provider contractual views, and a reproducibility settings block. Specification with worked panel screenshots and a build architecture.
* [Monthly Performance Report](Monthly_Performance_Report/monthly-performance-report.md) - The assembled monthly/quarterly reporting pack for owner, lender, and IE.
* [Performance Engineering Management](Performance_Engineering_Management/index.md) - The management layer: platform register set (daily review checklists, observations, actions, cases, outage events, engineering changes, knowledge base with document register), their schemas and linking model. A process specification whose recurring outputs are the daily checklist, the weekly action review, and the exclusions feed.
* [Dashboards](Dashboards/index.md) - Monitoring and diagnostic dashboards: design rules, the four-tier worked suite with screenshots, and the per-project register. Diagnostic instruments; the reports and the Outage Tracker ledger remain the numbers of record.
* [Grid Telemetry Mapping](Grid_Telemetry_Mapping/index.md) - The grid telemetry points (BESS ↔ grid operator), from the BESS SCADA's point of view: telemetry out, setpoints/commands in, with each point's source, the performance engineer's monitoring tag, and the grid-side address. The utility side of the interface is not owner-readable; this map is how the owner watches it anyway.
* [State of Energy Mapping](State_of_Energy_Mapping/index.md) - The remap from installed state of energy (OEM fleet) to contractual state of energy (the offtaker's 0-to-contracted range): buffers, the remap function block and its accounting gains, computation owner, and the layered counting/steering/recalibration algorithm.

## Status

* ⚠️ The Outage Tracker and Settlement Reconciliation carry **methodology as well as output**. The GADS event taxonomy and the reconciliation procedure are standing references that the Metrics Tree and Performance Guarantee Matrix depend on, so treat them as reference documents that happen to produce a recurring deliverable, not as report templates.
