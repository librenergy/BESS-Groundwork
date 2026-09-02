---
name: definitions-taxonomy
title: Definitions & Taxonomy — Global Glossary and Naming Conventions
type: Skill
description: Build or maintain the project's global glossary (Definitions(DEF)/definitions.md) and naming/coding conventions (taxonomy.md). Use whenever the user asks what an acronym or term means, wants a term added or defined ("add this to the definitions", "what does this acronym mean here"), reports naming confusion between documents, wants to assign organization codes or ID prefixes, or after new source documents are reviewed (new documents bring new terms). Also run a full-repo refresh when asked to "update the glossary" or when a satellite-document session surfaces undefined terms.
---

# Definitions & Taxonomy: Global Glossary and Naming Conventions

Two documents in `Definitions(DEF)/` serve every other document in the repo:

- **`definitions.md`** holds what terms *mean*: every acronym, term of art, and organization, defined once, globally, with the project-specific meaning (not the textbook one) and source references.
- **`taxonomy.md`** holds what terms *to use*: canonical names when synonyms exist, organization codes, and prefix/ID conventions for generated documents (interface IDs, guarantee IDs, metric codes, …). **Lives in `Ontology(ONT)/taxonomy.md` since 2026-08-31** (the ontology is the identifier authority); maintain it through the `ontology` session, with this skill handling the glossary side.

Rule of thumb: a reader who knows nothing about the project should be able to expand and understand any acronym in any repo document using `definitions.md` alone; a writer should be able to name and code anything new using `taxonomy.md` alone.

## The two tiers

Definitions live at two levels, and the distinction is what keeps them from becoming two competing glossaries:

| Tier | File | Holds | Written by |
|:---|:---|:---|:---|
| **1: Per-document extraction** | `Project_Documentation/<DOMAIN>/definitions.md` | Everything *that one document* defines, in its own words, with the clause reference. Source fidelity is the whole point; this is an extraction, like `guarantees.md`, not a glossary. | `project-document-review` |
| **2: Master glossary** | `Definitions(DEF)/definitions.md` | The repo-wide lookup: terms that travel beyond their own document, every acronym, and every collision between contracts. One place a reader goes to ask "what does this mean here?" | `definitions-taxonomy` (this skill) |

Tier 1 is the **source of record** for what a contract says. Tier 2 is the **single lookup**, and every entry it carries traces back to a Tier 1 file. Neither replaces the other: a term's full contractual definition stays in Tier 1; Tier 2 carries the project meaning plus the pointer.

**Tier 2 is never written from scratch once Tier 1 files exist**: it is rolled up from them (see below), then topped up by a repo scan for terms no contract defines (repo methodology terms, market vocabulary, equipment names from drawings).

### What gets promoted from Tier 1 to Tier 2

Promote a term when **any** of these is true:

- It is an **acronym**: no exceptions, however obvious.
- It is **used outside its own document**: in a satellite document, a data product, another contract's companion, or ordinary conversation about the project.
- It **collides**: another document defines the same word differently. Promotion is mandatory here, with ⚠️ and both meanings; these are the terms that cause disputes.
- It names **money, a guarantee, a measurement boundary, a clock, or an obligation with a deadline**.

Leave it in Tier 1 when it is **internal contract vocabulary that never leaves the document**: indemnity and arbitration boilerplate, standard definitional plumbing, test-protocol minutiae. A reader who needs those is already reading the contract, and promoting them buries the terms that matter.

When in doubt, promote compactly: one line in Tier 2 with the pointer, full detail left in Tier 1.

### Relinking

- Every Tier 2 entry's **Source cell links to the Tier 1 file** (with clause), so master → extraction → source PDF is a two-hop trail. Where a term predates its document's review, repoint it at the Tier 1 file when one appears.
- Each Tier 1 `definitions.md` carries a **one-line header pointing back** to the master glossary, so a reader who lands there mid-document knows where the cross-contract picture lives.
- A Tier 1 file that gets **updated triggers a roll-up in the same session**: new terms promoted, changed meanings corrected upstream, new collisions flagged. An extraction that has been re-reviewed while the master still shows the old meaning is the exact drift this structure exists to prevent.

## Principles

- **Project meaning over textbook meaning.** "Availability" gets the offtake-contract definition *and* the LTSA definition if they differ; the difference is the point. Cite the source (document, section/exhibit).
- **Flag the tricky ones.** Terms with two meanings in the repo (e.g. "SC" = Scheduling Coordinator *and* Substantial Completion) get an explicit ⚠️ ambiguity note listing both meanings and where each appears. These are the terms that cause real disputes.
- **Aliases are recorded, not repeated.** Source documents spell parties inconsistently (the same contractor may appear under three names across contracts). The taxonomy names one canonical form + code; definitions and new writing use only the canonical form, with aliases listed for searchability.
- **Capture, don't block.** Unknown expansion or unconfirmed entity → keep the entry with a ❓ marker and an open item; never guess a legal entity name.

## Building / refreshing the master `definitions.md`

Run this as a **roll-up**, in this order: the Tier 1 files first, because a contract's own words beat anything inferred from a summary:

1. **Collect every Tier 1 file**: `Project_Documentation/*/definitions.md` (and any per-document subfolder). These are the authoritative inputs.
2. **Promote per the rule above**, term by term, into the master. For each promoted term: project meaning in one to three lines, ⚠️ where it collides, and a **Source cell linking to the Tier 1 file and clause**.
3. **Then scan the rest of the repo** for terms no contract defines: summaries, guarantee extractions, matrices, drawings-derived equipment names, `project_info.md`, market and methodology vocabulary. Fan out with a search subagent if available. This is the top-up pass, not the primary one.
4. **What qualifies overall:** every acronym without exception (even obvious-looking ones: COD, POI, kW-month), plus any term of art whose *project* meaning isn't self-evident to an outsider: contract-defined words, telemetry flags, formula symbols, program names. Plain English that means exactly what it says stays out; the goal is unique definitions that need stating, not a dictionary.
5. **Group** entries: Organizations & parties · Contracts & commercial · Technical & equipment · Market & grid · Metrics · ⚠️ Ambiguous & tricky · Methodology/repo terms. Working category by category surfaces far more terms than one flat pass; the grouping itself prompts recall.
6. **Diff against the previous version** when refreshing: new terms from newly reviewed documents in, stale entries corrected, never silently dropped. Where a Tier 1 review *changed* a meaning (a re-read that sharpened or corrected it), the master entry is corrected too and the correction noted in the folder `log.md`.
7. **Check the collisions deliberately.** After promoting, re-read §6 (ambiguous terms): a new document usually adds at least one word that another contract already uses differently. Two contracts, two definitions, one word is the normal case on these projects, not the exception.
8. **Wire it in**: OKF frontmatter, folder `index.md` entry, `log.md` entry.

### When to run

- **After every source-document review** that produced or changed a Tier 1 `definitions.md`: same session, per the roll-up trigger above.
- **Before the satellite-document sequence starts** (the dedicated step after the EIM): all documents reviewed, so all Tier 1 files exist; this is the pass that produces the first complete master glossary and the taxonomy session that follows it.
- **On demand** when a session surfaces an undefined or contested term.

## Maintaining `taxonomy.md` (facilitated)

Taxonomy decisions are the user's to make: run them as short Q&A sessions, not unilateral edits:

1. **Organization codes**: propose codes for any uncoded org (2–5 chars, uppercase, code the company not the role); user confirms; then apply consistently in new documents.
2. **Canonical terms**: when two documents name the same thing differently, present the candidates, get a ruling, record loser terms as synonyms.
3. **ID prefixes**: agree the scheme *before* the satellite document that needs it (interface register, guarantee matrix, metrics tree) so IDs never need renumbering.

## Ongoing duties (all skills, all sessions)

- A source-document review that writes or updates a Tier 1 `definitions.md` → **roll up into the master glossary in the same session**, with the Source cells linked back.
- New acronym used in any new document → entry added to the master `definitions.md` in the same session.
- Never invent a new name for something the taxonomy already names.
- When a term's meaning is contested between contracts, the definitions entry records the conflict; resolving it belongs to the metrics-tree or performance-guarantee-matrix skills.
