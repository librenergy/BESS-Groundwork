# Data Products

Deliverables generated *from* the toolkit's documents and data — reports, dashboards, exports. Each product gets its own subfolder, **plain-named** (no acronym suffix inside an acronym-suffixed parent). New products are scoped through the [`data-product` skill](../.agents/skills/data-product/SKILL.md); the Monthly Performance Report, BESS GADS, and Settlement Reconciliation have their own dedicated skills.

Roughly in dependency order — the event ledger and the reconciliation supply the numbers the reports assemble.

* [BESS GADS](BESS_GADS/index.md) - GADS-aligned outage/derate event taxonomy. The taxonomy is a standing reference; the recurring output is the event ledger every availability calculation reads.
* [Settlement Reconciliation](Settlement_Reconciliation/index.md) - Shadow-settlement procedure verifying counterparty statements against meter data and awards; the recurring output is the monthly reconciliation.
* [Daily Performance Report](Daily_Performance_Report/daily-performance-report.md) - Availability under each contractual definition (equipment, power, operational, combined) with excused and depth-weighted variants, energy and efficiency, data quality, commercial events, and a reproducibility parameter block. Markdown specification plus an HTML render skeleton.
* [Monthly Performance Report](Monthly_Performance_Report/monthly-performance-report.md) - The assembled monthly/quarterly reporting pack for owner, lender, and IE.

## Status

* **2026-08-10** — `BESS_GADS` and `Settlement_Reconciliation` moved in from the repo root (acronym suffixes dropped on the way in), on the basis that each is defined by what it recurs into. ⚠️ Both carry **methodology as well as output** — the GADS event taxonomy and the reconciliation procedure are standing references that the Metrics Tree and Performance Guarantee Matrix depend on, so treat them as reference documents that happen to produce a recurring deliverable, not as report templates.
