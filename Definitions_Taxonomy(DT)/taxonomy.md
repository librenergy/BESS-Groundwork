---
type: Template
title: Taxonomy — Naming, Codes & Prefix Conventions
description: "How things are named across the toolkit: canonical equipment/system terms, organization codes, and the prefix/ID conventions every generated document must follow."
---

# Taxonomy — Naming, Codes & Prefix Conventions

**Status:** template — populated in a facilitated session (skill: `definitions-taxonomy`). The [definitions.md](./definitions.md) file defines *what terms mean*; this file defines *which term to use* and *how to code it*.

Every document generated from the EIM (registers, matrices, reports) carries codes and prefixes. This file is the single authority for those conventions — when two documents disagree on a name or code, this file wins, and the divergent document gets fixed.

## 1. Organization codes

One short, stable code per organization. Used in document IDs, interface IDs, contact registers, and anywhere a party is referenced in structured form. Rules:

- 2–5 characters, uppercase, unique within the project.
- Code the **company**, not the role (roles change; one utility may be offtaker, TO, and BA at once).
- Record every alias/spelling seen in source documents so searches don't miss them.

| Code | Legal name | Short name | Roles on this project | Aliases seen in documents |
|------|-----------|------------|----------------------|---------------------------|
| ❓ | | | | |

> ❓ = code not yet assigned — assign in session and apply consistently in new documents.

## 2. Canonical equipment & system terms

When several names exist for the same thing, pick one canonical term; the others become listed synonyms (kept for search, never used in new writing). Typical candidates: the EMS vs the vendor's controller product name; the plant controller vs SCADA vs RTAC; "battery enclosure" vs the OEM product name; PCS when integrated vs standalone.

| Canonical term | Covers / replaces | Notes |
|----------------|-------------------|-------|
| ❓ | | |

## 2b. Document & folder acronyms

Ratified 2026-07-26 and **applied to the folder names** as `Folder_Name(ACR)` for easier navigation. Markdown links to these paths must percent-encode the parentheses (`%28`/`%29`). `Project_Documentation` keeps its name (scaffolding). The former `KPI_Definitions` was merged into `Metrics_Tree(MT)` as `metrics-and-kpis.md`.

**Acronym suffixes are top-level only.** A folder nested inside an acronym-suffixed folder uses its plain name — no second acronym in the path. So the products under `Data_Product(DP)/` are plain-named (`Monthly_Performance_Report/`), not `…(MPR)`. Stacked acronyms in one path are harder to read than the names they abbreviate.

| Acronym | Folder |
|---------|--------|
| EIM | Entity_Interaction_Map(EIM) |
| CR | Contact_Register(CR) |
| DIR | Data_Interface_Register(DIR) |
| PGM | Performance_Guarantee_Matrix(PGM) |
| WOM | Warranty_Obligation_Matrix(WOM) |
| ECP | Escalation_Communication_Plan(ECP) |
| OAP | BESS_GADS(GADS) |
| DP | Data_Product(DP) — parent folder for generated deliverables; its product subfolders are plain-named |
| SR | Settlement_Reconciliation(SR) |
| MT | Metrics_Tree(MT) |
| DT | Definitions_Taxonomy(DT) |

## 3. Prefix & ID conventions

How structured identifiers are built in generated documents. Agree the scheme **before** the satellite document that needs it, so IDs never need renumbering.

| Scope | Convention | Example |
|-------|------------|---------|
| EIM node IDs | stable uppercase snake IDs, never renamed once satellite docs reference them | `BESS_LTSA`, `OM_PROV` |
| Interface IDs (Data Interface Register) | ❓ e.g. `IF-<ORG>-<NN>` | |
| Guarantee IDs (Performance Guarantee Matrix) | ❓ e.g. `PG-<contract>-<NN>` | |
| Warranty IDs (Warranty Obligation Matrix) | ❓ e.g. `W-<NN>` | |
| KPI IDs | ❓ e.g. `KPI-<NN>` | |
| Document versions | `vMAJOR.MINOR`, draft until v1.0; satellite docs pin `EIM_VERSION` | |

## 4. File & folder naming

- Folders: `Title_Case_With_Underscores(ACRONYM)` where an acronym is ratified (§2b), e.g. `Data_Interface_Register(DIR)/`; percent-encode the parentheses in markdown links.
- Documents: kebab-case `.md` (e.g. `warranty-obligation-matrix.md`); source documents keep their original filenames for citability.
- Every folder carries an `index.md`; every non-reserved `.md` carries OKF frontmatter.

## Open questions

- [ ] Canonical project-name spelling (check how client documents spell it vs internal files).
- [ ] Assign organization codes (section 1) and confirm legal entity names.
- [ ] Agree ID prefix schemes per satellite document before its build session.
