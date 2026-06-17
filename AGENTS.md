# AGENTS.md

Entry point for any AI agent working in this repository — Claude Code, Codex, Cursor, or anything else.

This is a **documentation-only** toolkit for performance engineering and asset management on Battery Energy Storage System (BESS) assets: Markdown templates plus facilitated skills that populate them. There is no build and no test suite — the documents are the deliverable.

## Start here

1. **Read [`README.md`](./README.md).** It indexes every template, the recommended build sequence, and which skill builds each document. Everything anchors on the **Entity Interaction Map (EIM)**; nine satellite documents derive from it.
2. **To build or update a document, run its skill** under [`.agents/skills/`](./.agents/skills/). Each skill (`<name>/SKILL.md`) is a facilitated Q&A session: it interviews the user, asks the questions needed to ground the document in the real project, populates the matching template, and saves a project-specific copy alongside the base. The README's Resources table links each document to its skill.

The Contact Register (00) has no skill — fill its template directly.
