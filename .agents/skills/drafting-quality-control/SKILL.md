---
name: drafting-quality-control
title: Drafting Quality Control
type: Skill
description: Post-draft cleanup pass for any substantial document. Removes em dashes in favor of plain punctuation and cuts verbosity so a human can read the result quickly. Run automatically after drafting or substantially rewriting a document, before registering and logging it; also on request ("run drafting QC", "clean this draft up", "too wordy").
---

# Drafting Quality Control

Agents draft in a recognizable style: em dashes everywhere, long sentences, restated context, meta-commentary. This skill is the corrective pass. **Run it after every big draft** (a new document, a major rewrite, a companion set), before the draft is registered in indexes and logged.

Three passes, in order. Edit the file in place.

## Pass 1: em dashes

House rule: **no em dashes in prose.** Replace each one by what it is doing:

| The em dash is acting as | Replace with | Example |
|:---|:---|:---|
| A joint between two independent clauses | Period (usually) or semicolon | "The log is the record — it never gets swept" → "The log is the record. It never gets swept." |
| A lead-in to a definition, list, or explanation | Colon | "Three registers — observations, actions, cases" → "Three registers: observations, actions, cases" |
| A parenthetical pair | Commas, or parentheses if truly an aside | "the flag — set weekly — drives the report" → "the flag, set weekly, drives the report" |
| Emphasis before a punchline | Comma, or restructure the sentence | Often the sentence is better split anyway |
| A range between numbers or dates | En dash or "to" | "20—40%" → "20–40%" |

Keep, do not replace:

- A standalone `—` used as an **empty-cell placeholder in a table** (e.g. the README Resources "Skill" column). That is convention.
- **Quoted source text.** Contract and document quotes keep their original punctuation.
- **`log.md` files.** Historical entries are never swept.

Verify: search the file for `—` when done. Every survivor must be a placeholder or a quote.

## Pass 2: verbosity

Goal: a human reads it easily. Detail survives only where it changes what the reader does.

Work section by section and cut:

1. **Meta-commentary.** "This is the key design", "the point here is", "importantly". State the thing; do not announce it.
2. **Restated context.** Anything the reader already has from the section above or the linked document.
3. **Double examples.** Where one example carries the point, the second is padding.
4. **Framing prose around tables.** If the table says it, the sentence before it should not.
5. **Intensifiers and hedges.** "Very", "quite", "essentially", "it is worth noting that".
6. **Long sentences.** One idea per sentence; split anything over roughly 30 words.

Prefer tables and bullets for enumerable facts, with short cells. Prefer prose only where reasoning connects facts.

**What must survive untouched:** every fact, number, obligation, cited clause, defined term, cross-reference, and table of record. Losing substance is failure, not concision. A typical first draft shrinks 20–40% with nothing lost; if a section will not shrink, it was probably already tight.

## Pass 3: mechanical checks

Run after Pass 1 and Pass 2. These are fast pattern checks that break links or portability if missed.

### Markdown image paths must be relative

In Markdown files, image references must use **relative paths** from the document's location, never absolute filesystem paths.

| Wrong | Right |
|:---|:---|
| `![alt](/Users/name/project/repo/img.png)` | `![alt](./img.png)` or `![alt](../img.png)` |
| `![alt](file:///Users/name/.../img.png)` | `![alt](./assets/img.png)` |

Absolute paths break on every other machine and in every rendered view (GitHub, GitLab, Obsidian, generated sites). Verify with a regex search for `]\(` followed by `/Users`, `/home`, `file:///`, or any leading `/` before an image filename.

### ...

(Additional mechanical checks live here as they accumulate.)

## Close

Reread the document once end to end as its intended reader. Then proceed to the normal registration steps (index, log). Mention the QC pass in the same log entry as the draft; it needs no entry of its own.
