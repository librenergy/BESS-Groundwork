---
name: eim-review-build
title: EIM Review & Build — Facilitated Session
type: Skill
description: Facilitate a structured Q&A session to review, build, or update a BESS project's Entity Interaction Map (EIM) in Mermaid. Use this skill whenever the user wants to create an EIM for a new project, review or validate an existing EIM with an asset manager or owner, onboard a new BESS client, map project stakeholders/contracts/data flows, or says things like "let's go through the interaction map", "map out the entities for [project]", or "kickoff session". This is the FIRST skill in the BESS asset-management toolkit — always offer it before any satellite-document skill if no project-specific EIM exists yet, because every other document (interface register, RACI & escalation, guarantee & warranty matrices, metrics & KPIs, Outage Tracker, settlement reconciliation, monthly report) is derived from the EIM.
---

# EIM Review & Build — Facilitated Session

> **Before you start:** read `Definitions_Taxonomy(DT)/definitions.md` (what every acronym and term means on this project — ⚠️-flagged entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary throughout, and add any new acronym or term this session introduces to the glossary before wrapping up (`definitions-taxonomy` skill).

## What this skill does

Run a collaborative, section-by-section working session that produces (or updates) a project-specific Entity Interaction Map: a Mermaid flowchart showing every entity (companies, contracts, assets, data systems) and every interaction (contractual, operational, data flow) on a BESS project. The toolkit is BESS-first, but the method is asset-agnostic — for a PV plant, swap the battery asset detail for array/inverter blocks and the same rings (ownership, EPC/warranty, O&M, market, grid, controls) apply.

The generic baseline EIM lives in `Entity_Interaction_Map(EIM)/entity-interaction-map.md` (repo root) — the Mermaid diagram is inline in that file. A session instantiates it for a specific project with real company names, real contract structures, and project-specific deviations.

## Working copy

The working copy is the Mermaid block inline in `Entity_Interaction_Map(EIM)/entity-interaction-map.md`, and **the repo copy is always the source of truth**. A cloud Mermaid editor (Mermaid Chart / Mermaid.ai, editable via MCP) is useful for drafting because it is easier to read and pan, but it is a convenience copy: link it from a callout at the top of the document, never let the repo hold only a pointer, and re-sync the inline block after any cloud edit. Access to a cloud editor depends on a subscription that may lapse, so nothing in the repo may depend on it. Two rules when editing the cloud copy: **re-fetch it before every edit** (concurrent editor sessions overwrite each other; lost updates do not merge, and this has already happened once), and after pushing an edit via MCP, tell the user to refresh their editor tab before making changes there.

## Session principles

- **One section at a time.** Never dump 20 questions. Ask 2–4 focused questions per section, render the updated map fragment, confirm, move on.
- **Edit live.** After each answer, update the Mermaid and show the affected section (or the whole map if small) so the user reacts to a picture, not prose. Render via an inline visual or artifact when the interface supports it.
- **Capture, don't block.** If the user doesn't know an answer (e.g., "who holds the GIA?"), add the node with a `❓` marker and log it in the open-questions list rather than stalling the session.
- **Contractual accuracy over visual completeness.** A wrong edge (e.g., warranty claims going direct to OEM when the contract routes via the LTSA) is worse than a missing node. When the user states a relationship, reflect back its contractual meaning to confirm.
- **Respect the legend.** Keep the established classDefs: `owner` (black), `company` (green), `data` (purple), `asset` (blue), `person` (orange). New node types get a new classDef, agreed with the user.

## Section walkthrough (default order)

Work through these in order unless the user redirects. For each: state what the baseline assumes, ask the project-specific questions, edit, confirm.

### 1. Project frame
- Project name, market/ISO, COD status (operating / commissioning / construction)?
- MW / MWh, number of enclosures & PCS skids (affects asset detail level)?
- Who is the Asset Owner entity (the actual SPV name)?

### 2. Ownership & finance ring
- Asset Manager: internal to owner or third party? Same entity as owner?
- Lender/investor structure? Independent Engineer engaged?
- Insurer relationship managed by AM or AO directly?
- Land: lease or owned?

### 3. EPC & warranty structure
- Single EPC or split BESS/substation EPCs?
- Who manages workmanship warranty claims — LTSA or owner directly?
- Battery/PCS/EMS supplied through EPC or owner-furnished equipment? (Changes warranty privity — draw the edges accordingly.)

### 4. O&M / LTSA ring
- One LTSA for everything, or split BESS vs. substation/BOP?
- Is the LTSA the battery OEM itself? (Common — collapses nodes.)
- ROCC: LTSA's, owner's, or third party? 24/7?
- Which CMMS and ticketing systems actually exist, and who has access?

### 5. Market & revenue ring
- Revenue model: tolling, full merchant, hybrid? Offtaker identity?
- Optimizer and SC/QSE: same company or separate? Route-to-market provider?
- Who is the registered Generator Operator? (Often the LTSA or AM — verify, it drives the RACI later.)
- Standing cadences: optimizer daily call / weekly report — confirm or correct.

### 6. Grid & ISO ring
- ISO/BA, Transmission Owner, TOP — names.
- GIA counterparty and any special interconnection conditions worth a labeled edge.
- ISO telemetry signal set: confirm APD/APC/SOC/MAXENER/Mode applies in this market, or adapt (e.g., ERCOT vs. CAISO naming).

### 7. On-site control & data architecture
- PPC vendor, EMS vendor, RTAC setup — confirm the control chain edges (BMS → battery controller → PPC; EMS ↔ PPC mode authority).
- Where does the owner's data platform / APM tap in? (EMS API? Direct PLC/Modbus? Historian replication?) Draw that edge precisely — it scopes the data engagement.
- Revenue metering: at POI only, or also PCS-level check meters?

### 8. Performance engineering overlay (optional)
- If a performance-engineering consultant is engaged, add the PE entity and its data/reporting edges.

## Wrap-up (always do)

1. Show the complete updated Mermaid map.
2. Produce the **open questions list** (every ❓) with a suggested owner for each answer.
3. Save/deliver: `Entity_Interaction_Map(EIM)/entity-interaction-map.md` updated in place (version bumped); record the open-questions list in the folder's `todo.md` and the session's changes in the folder's `log.md`.
4. Offer next steps explicitly: "From this map we can now generate the remaining documents — recommended order: (1) Warranty Obligation Matrix, (2) Performance Guarantee Matrix, (3) RACI (including its escalation & communication sections), (4) Data Interface Register, (5) Metrics Tree & KPIs; then the data products in `Data_Product(DP)/`, in dependency order: (6) Outage Tracker — the event ledger the reports read, so it comes first — (7) Daily Performance Report, (8) Monthly Performance Report, (9) any counterparty data exchange, (10) Settlement Reconciliation. Each has its own skill and template." (Definitions & Taxonomy and the Contact Register precede the map in the build sequence — confirm they are done rather than offering them here.)

## Editing an existing map

If the user brings an existing EIM (like the baseline), diff-style review: walk the same sections but phrase questions as confirmations ("The map shows warranty management routed through the BESS LTSA — is that contractually accurate for this project?"). Flag visual/structural issues (duplicate node labels, e.g., a PCS OEM CMMS node mislabeled "Bat OEM CMMS"; dangling nodes; bidirectional edges that should be one-way) as you encounter them.

## Mermaid hygiene

- Keep node IDs stable across versions (downstream documents reference them).
- Use `===` for contractual relationships, `-->`/`<-->` for operational/data flows, labeled edges for named agreements.
- Add a version comment header: `%% EIM {{project}} v{{n}} — {{date}}`.
