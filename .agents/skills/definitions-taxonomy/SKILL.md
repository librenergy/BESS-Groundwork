---
name: definitions-taxonomy
title: Definitions & Taxonomy — Global Glossary and Naming Conventions
type: Skill
description: Build or maintain the project's global glossary (Definitions_Taxonomy(DT)/definitions.md) and naming/coding conventions (taxonomy.md). Use whenever the user asks what an acronym or term means, wants a term added or defined ("add this to the definitions", "what does this acronym mean here"), reports naming confusion between documents, wants to assign organization codes or ID prefixes, or after new source documents are reviewed (new documents bring new terms). Also run a full-repo refresh when asked to "update the glossary" or when a satellite-document session surfaces undefined terms.
---

# Definitions & Taxonomy — Global Glossary and Naming Conventions

Two documents in `Definitions_Taxonomy(DT)/` serve every other document in the repo:

- **`definitions.md`** — what terms *mean*: every acronym, term of art, and organization, defined once, globally, with the project-specific meaning (not the textbook one) and source references.
- **`taxonomy.md`** — what terms *to use*: canonical names when synonyms exist, organization codes, and prefix/ID conventions for generated documents (interface IDs, guarantee IDs, KPI IDs, …).

Rule of thumb: a reader who knows nothing about the project should be able to expand and understand any acronym in any repo document using `definitions.md` alone; a writer should be able to name and code anything new using `taxonomy.md` alone.

## Principles

- **Global scope.** One glossary for the whole repo — never per-document glossaries. Documents may keep short local "definitions" sections only for math-heavy KPI formulas; everything else points here.
- **Project meaning over textbook meaning.** "Availability" gets the offtake-contract definition *and* the LTSA definition if they differ — the difference is the point. Cite the source (document, section/exhibit).
- **Flag the tricky ones.** Terms with two meanings in the repo (e.g. "SC" = Scheduling Coordinator *and* Substantial Completion) get an explicit ⚠️ ambiguity note listing both meanings and where each appears. These are the terms that cause real disputes.
- **Aliases are recorded, not repeated.** Source documents spell parties inconsistently (the same contractor may appear under three names across contracts). The taxonomy names one canonical form + code; definitions and new writing use only the canonical form, with aliases listed for searchability.
- **Capture, don't block.** Unknown expansion or unconfirmed entity → keep the entry with a ❓ marker and an open item; never guess a legal entity name.

## Building / refreshing `definitions.md`

1. **Scan the whole repo** — all `.md` files (summaries, guarantee extractions, matrices, skills, `project_info.md`), plus terms appearing in source-document filenames and index descriptions. Fan out with a search subagent if available.
2. **What qualifies:** every acronym without exception (even obvious-looking ones — COD, POI, kW-month), plus any term of art whose *project* meaning isn't self-evident to an outsider: contract-defined words, telemetry flags, formula symbols, program names. Plain English that means exactly what it says stays out — the goal is unique definitions that need stating, not a dictionary.
3. **Group** entries: Organizations & parties · Contracts & commercial · Technical & equipment · Market & grid · Metrics & KPIs · Methodology/repo terms. Working category by category surfaces far more terms than one flat pass — the grouping itself prompts recall. Alphabetical within a group.
4. **Define in project context**, one entry per term: `**TERM** — expansion. One-to-three-line meaning as used on this project. *Source: file/§.*` Add ⚠️ notes for ambiguous terms and cross-reference the authoritative document for contested definitions.
5. **Diff against the previous version** when refreshing: new terms from newly reviewed documents in, stale entries corrected, never silently dropped.
6. **Wire it in**: OKF frontmatter, folder `index.md` entry, `log.md` entry.

## Maintaining `taxonomy.md` (facilitated)

Taxonomy decisions are the user's to make — run them as short Q&A sessions, not unilateral edits:

1. **Organization codes**: propose codes for any uncoded org (2–5 chars, uppercase, code the company not the role); user confirms; then apply consistently in new documents.
2. **Canonical terms**: when two documents name the same thing differently, present the candidates, get a ruling, record loser terms as synonyms.
3. **ID prefixes**: agree the scheme *before* the satellite document that needs it (interface register, guarantee matrix, metrics & KPIs) so IDs never need renumbering.

## Ongoing duties (all skills, all sessions)

- New acronym used in any new document → entry added to `definitions.md` in the same session.
- Never invent a new name for something the taxonomy already names.
- When a term's meaning is contested between contracts, the definitions entry records the conflict — resolving it belongs to the KPI-definitions or guarantee-matrix skills.
