---
name: template-sync
title: Template Sync — Pull Master-Template Updates into a Project Clone
type: Skill
description: Check whether the master template has updates this project clone hasn't received, and apply them safely. Use whenever the user asks "is there an update to the template?", "sync from the master", "pull template changes", or when a clone predates a known toolkit improvement. Handles upstream-remote setup, reports what changed using the template's own log, applies method files three-way (never overwriting local improvements), and flags clone-side improvements as port-back candidates.
---

# Template Sync

Project clones share **no git history** with the master template (deliberately; clones hold confidential content and must stay out of the public repo's network), so `git merge` can never bring template updates in. This skill is the replacement: content-level sync with three-way safety, driven by a marker recording the last-synced upstream commit.

## Step 0: Upstream remote

Check `git remote -v` for `upstream`. If missing, add it and fetch:

```sh
git remote add upstream https://github.com/librenergy/bess-performance-engineering.git
git fetch upstream
```

(HTTPS by default: it works read-only everywhere; use an SSH alias only if the environment already has one configured.) **Never push to upstream from a clone.**

## Step 1: What's new?

The clone tracks its sync state in **`.template-sync`** at the repo root (committed; one line: `upstream_commit: <sha>` plus a date). Then:

- **Marker exists:** `git log --oneline <marker-sha>..upstream/main` lists the template commits the clone hasn't seen, and `git diff <marker-sha> upstream/main -- log.md` tells the human-readable story; the template's own log **is** its changelog. Summarize both for the user before touching anything.
- **No marker (first run / older clone):** adoption mode. There is no base to diff against, so report the divergence file-by-file (Step 2 buckets) and bootstrap the marker once the user has decided what to take.

## Step 2: The three buckets

Every file in the clone belongs to exactly one bucket. **Which bucket decides what the sync may do:**

| Bucket | Files | Sync behavior |
|---|---|---|
| **1: Method files** (meant to stay identical to the master) | `.agents/skills/**`, `AGENTS.md`, `Project_Documentation/*/instructions.md`, `Performance_Guarantee_Matrix(PGM)/calculations/index.md` + `calc-sheet-template.md`, `LICENSE` | Sync three-way (Step 3) |
| **2: Status-bearing structure** (template shape + clone status interleaved) | `README.md`, `INSTRUCTIONS.md`, root `index.md`, `Project_Documentation/index.md`, `Data_Product(DP)/index.md`, `Ontology(ONT)/taxonomy.md` structural sections, plus **any template document the clone has not yet populated** (still carries `{{PLACEHOLDER}}`s), which may be taken wholesale | Diff, then merge by hand with the user; never overwrite |
| **3: Project content** (never synced) | Populated documents, `project_info.md`, everything in `Project_Documentation/` except `instructions.md`, all `log.md`/`todo.md` files, the glossary | Untouchable. Improvements here flow the **other** way: genericize and port to the master (see Step 5) |

Structural changes (folders moved or renamed upstream) are applied as moves in the clone, with the full three-pass reference sweep the AGENTS.md conventions require.

## Step 3: Three-way apply for method files

For each Bucket-1 file, compare three versions: **base** (the file at `upstream/<marker-sha>`), **local**, and **upstream** (`upstream/main`):

- local == base, upstream changed → **fast-forward**: take upstream's file.
- local changed, upstream == base → **clone-side improvement**: do *not* revert it; flag it as a **port-back candidate** (Step 5).
- both changed → **merge conversation**: show both diffs and merge with the user. (Precedent: a clone's hand-written review guide once carried method the template lacked; the right outcome was a merge that improved *both* sides, not an overwrite in either direction.)
- **Uncommitted local changes in any Bucket-1 file → stop and require a commit or stash first.** A sync must never be the thing that destroys work in progress.

## Step 4: Close out

Update `.template-sync` to the fetched `upstream/main` sha; commit the synced files with a message naming the upstream commit range; write a folder-appropriate `log.md` entry (what was taken, what was deferred, what conflicted).

## Step 5: The reverse direction

Anything this sync surfaces as a clone-side improvement to a method file (or anything the user wants to *suggest* to the template) does not go by push or PR from the clone (unrelated histories, confidential repo). It goes by the porting workflow: **genericize** (names and figures become patterns; run the `genericity-audit` skill), apply to the master's working tree, and record it in the clone's `MASTER_TEMPLATE_TODO.md`. Outside contributors without a clone fork the master on GitHub and PR normally.

## Judgment notes

- The report comes **before** any change: what's new, per bucket, with the log story; then the user chooses.
- A clone far behind the template (many structural changes) is better synced structure-first (folder moves), then Bucket 1, then Bucket 2; one commit per stage, so each is reviewable.
- If `upstream/main` on the public remote lags the maintainer's local master, say so in the report rather than syncing twice.
