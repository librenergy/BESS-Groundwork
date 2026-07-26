---
type: Guide
title: Agent Entry Point
description: "Entry point for any AI agent working in this repository — what the toolkit is, how documents get built, and the OKF conventions every change must follow."
---

# AGENTS.md

Entry point for any AI agent working in this repository — Claude Code, Codex, Cursor, or anything else.

This is a **documentation-only** toolkit for performance engineering and asset management on Battery Energy Storage System (BESS) assets: Markdown templates plus facilitated skills that populate them. There is no build and no test suite — the documents are the deliverable.

## Start here

1. **Read [`README.md`](./README.md).** It indexes every template, the recommended build sequence, and which skill builds each document. Everything anchors on the **Entity Interaction Map (EIM)**; the satellite documents derive from it.
2. **To build or update a document, run its skill** under [`.agents/skills/`](./.agents/skills/). Each skill (`<name>/SKILL.md`) is a facilitated Q&A session: it interviews the user, asks the questions needed to ground the document in the real project, populates the matching template **in place** (the project clone carries the populated version; pristine bases live in the master template repo). The README's Resources table links each document to its skill.

The Contact Register has no skill — fill its template directly.

## Conventions (OKF)

**This repository follows [OKF v0.2](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) (Google's Open Knowledge Format).** Every change must keep it conformant:

- Every non-reserved `.md` file carries YAML frontmatter with a non-empty `type` (`title` and `description` recommended). The reserved root `index.md` (section-grouped `* [Title](url) - description` bullets) and `log.md` (date-grouped entries, newest first) carry no frontmatter.
- When adding or materially changing a document: add frontmatter, update the relevant `index.md`, and add a `log.md` entry.
- **Definitions & taxonomy first:** before working in any document, consult `Definitions_Taxonomy(DT)/definitions.md` (project meaning of every acronym/term; ⚠️ entries carry two meanings) and `Definitions_Taxonomy(DT)/taxonomy.md` (canonical names, organization codes, ID prefixes), if populated. Use the established vocabulary; when you introduce a new acronym or term, add it to the glossary in the same session (`definitions-taxonomy` skill).
- **Per-folder indexes:** if you are working in a folder that has no `index.md`, create one — a bullet list (`* [Title](url) - description`) covering the folder's files, no frontmatter — so every folder you touch stays navigable. Non-markdown source documents (PDF/DOCX/XLSX) are listed for inventory even though they are not OKF concept documents.
- **Per-folder logs:** folders whose documents evolve materially keep their own `log.md` (same date-grouped structure as the root log, newest first). **All edit history lives in `log.md` — never in the main documents** (no version-history blocks or changelogs in document bodies). Write the detailed change entry in the folder's `log.md` (create it on first material change); the root `log.md` carries repo-wide and cross-cutting changes, plus one-line pointers to major folder milestones.
- **Per-folder todos:** anything that is a task — open items, pending questions, unresolved conflicts — goes in the folder's `todo.md` (checklist, one item per line with context and a suggested owner), never in the document body. If the folder has no `todo.md` yet, create it with the first task; do **not** create empty `todo.md` files for folders with nothing pending. The main document stays a clean formal version; it may keep a one-line "Open items: tracked in this folder's todo.md" pointer.
