---
name: monthly-performance-report
title: Monthly Asset Performance Report — Facilitated Session
type: Skill
description: Set up or produce a BESS project's monthly asset performance report through a facilitated session with the asset manager. Use whenever the user wants to design the monthly/quarterly reporting pack, define what goes to the owner/lender/IE, assemble a specific month's report from platform data, or automate report generation (Marimo/PDF pipelines). Two modes - template-setup (first time, tailoring sections to the project) and monthly-production (filling a given month). Requires the project EIM; Metrics & KPIs and BESS GADS make the numbers defensible (run those skills first for the setup mode). The report is a data product — it lives under Data_Product(DP)/.
---

# Monthly Asset Performance Report — Facilitated Session

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

Template: `Data_Product(DP)/Monthly_Performance_Report/monthly-performance-report.md` (repo root). Every section header in the template names its feeding systems per the EIM — the report is an assembly of the other satellite documents' outputs.

## Two modes

**Mode A — Template setup (first session on a project):**
1. Confirm audience and distribution (AO only? lender? IE? offtaker gets a variant?) — audiences change tone and content.
2. Walk the template's sections; keep/cut/add per project. For each kept section, verify its feeding source exists in the Data Interface Register and its metrics exist in Metrics & KPIs (`Metrics_Tree(MT)/metrics-tree.md`). Missing feeds become the automation backlog.
3. Agree the production calendar: data cutoff, draft date, review, publication — anchored to settlement and LTSA-report availability.
4. Decide the production pipeline: manual, notebook-generated (e.g., Marimo → PDF), or hybrid; record where each section's numbers come from programmatically.

**Mode B — Monthly production (recurring):**
1. Pull the month's reconciled availability (the BESS GADS reconciled month), KPI values vs targets, settlement reconciliation summary, ticket stats, and warranty positions.
2. Fill the template section by section; the user supplies narrative judgment (executive summary, issue commentary), the data fills the tables.
3. Flag any number that fails its reconciliation tolerance — never publish an unreconciled figure silently; annotate it.
4. Produce the Actions & Decisions table — a report without asks is a newsletter.

## Facilitation guidance

- The executive summary is written last and is the only part many recipients read: status colour, three numbers, three issues, asks.
- Trend over snapshot: every table should carry YTD and target columns; degradation and cycles need cumulative views.
- Keep lender/IE variants consistent with the owner version — same numbers, different depth. Divergent numbers across audiences is how credibility dies.

## Outputs

- Mode A: `Data_Product(DP)/Monthly_Performance_Report/monthly-performance-report.md` tailored in place + automation backlog + production calendar.
- Mode B: `{{project}}-performance-report-{{YYYY-MM}}.md` (or PDF via the agreed pipeline).
