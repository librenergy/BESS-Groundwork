---
name: data-product
title: Data Product — Scoping & Build Session
type: Skill
description: Scope and register a new data product in Data_Product(DP)/ — any recurring deliverable generated from the toolkit's documents and plant data (reports, dashboards, evidence packs, settlement exports). Use whenever the user wants to add a data product, define its audience/cadence/sources, or review the data-product catalogue. The Monthly Performance Report is the first instance and has its own dedicated skill (monthly-performance-report); use this skill for every other product and for the category itself.
---

# Data Product — Scoping & Build Session

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

A **data product** is a recurring, consumable deliverable *generated from* the toolkit's documents and plant data — a report, dashboard, evidence pack, or export. The documents (EIM, PGM, Metrics & KPIs, BESS GADS…) define what is true; data products package it for an audience on a cadence. Each product lives in its own subfolder under `Data_Product(DP)/` and is listed in the folder `index.md`.

Typical products: monthly performance report (has its own skill), performance dashboards, the OEM-LD evidence pack (event log + shadow calc backing an availability LD claim), shadow-settlement exports, lender/IE quarterly packs, NERC GADS export.

## Prerequisites

- The documents the product draws from — at minimum the Metrics & KPIs document (authoritative calculations) and the Data Interface Register (where the numbers come from). If missing, run those skills first or capture ad hoc and flag.

## Session flow

1. **Definition card.** Audience (who consumes it), purpose (the decision or obligation it serves), cadence/trigger (monthly, per-event, on-demand), and delivery format (PDF, dashboard, CSV export, portal).
2. **Contractual anchor.** Is the product required by a contract (reporting obligation, claim evidence, deadline, deemed-acceptance window)? Cite the clause — a contractual product inherits its deadline and format from the contract, not from preference.
3. **Inputs.** Which metrics (by name, from Metrics & KPIs), which event records (BESS GADS), which interfaces (DIR `SYS`/`IF` refs). Every number in the product must trace to an authoritative source already declared — a product is assembly, never a new calculation.
4. **Ownership.** Who produces it, who reviews it, who signs/sends it, who receives it — align with the RACI Matrix; flag mismatches back to that document.
5. **Skeleton.** Draft the product's structure (sections for a report, panels for a dashboard, columns for an export) as `Data_Product(DP)/<Product_Name(ACR)>/` with its own `index.md` and main document; register the acronym in the taxonomy.
6. **Register.** Add the product to `Data_Product(DP)/index.md` and the repo README; note automation status (manual, scripted, platform-generated) in the product doc.

## Facilitation guidance

- One product, one audience — a pack that serves the lender and the OEM at once serves neither; split it.
- Push computation upstream: if the session uncovers a needed calculation, it belongs in Metrics & KPIs (define it there first), not inline in the product.
- For claim-evidence products, the evidentiary standard is the contract's — contemporaneous records, timestamps, the counterparty's own formulas.

## Outputs

1. New product subfolder under `Data_Product(DP)/` (index + skeleton document), registered in the folder `index.md`; folder `todo.md`/`log.md` updated.
2. Automation backlog entries for anything not yet producible from existing data.
