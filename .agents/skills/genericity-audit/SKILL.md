---
name: genericity-audit
title: Genericity Audit — No Project-Specific or Confidential Content
type: Skill
description: Audit the template repo for project-specific and confidential content before it is committed, PR'd, merged, or pushed. Use whenever the user asks to "check for confidential information", "run the genericity audit", "make sure nothing project-specific is in here", or before contributing content ported from a project clone. Scans the working tree (all file types, including untracked-committable files and log.md) and the full git history, against generic pattern classes plus the contributor's private, gitignored wordlist.
---

# Genericity Audit

This repo is a **public template**. Everything committed here — including `log.md` and the full git history — publishes. Content arrives by porting lessons from real project clones, which is exactly how confidential material leaks: a worked example that quotes a real figure, a vendor name used as an "e.g.", a contract-window number distinctive enough to identify the deal. **Run this audit before any commit, PR, merge, or push that adds ported or substantially edited content.**

## The vocabulary — public patterns + private wordlist

The audit needs two inputs:

1. **Generic pattern classes** (below) — public, part of this skill.
2. **The contributor's private wordlist**: `.audit-wordlist.txt` at the repo root, **gitignored, never committed**. One term or regex per line, `#` comments allowed. Each contributor maintains their own from the projects they have worked on: client and project names and codes, counterparty legal entities, vendor and product names, named individuals, site/place names, and any exact figures from executed contracts. If the file is missing, create it before auditing — an audit without the names of the projects you actually worked on is theater.

## Scope — three surfaces, all mandatory

1. **Working tree, every committable file** — `git ls-files -co --exclude-standard`. All file types, not just `.md`: HTML skeletons, JSON samples, CSVs, and code comments carry leaks that Markdown sweeps miss.
2. **`log.md` files** — they publish with the repo. (Never *sweep-edit* them per the AGENTS.md rule; but they are absolutely in audit scope.)
3. **Full git history — every blob ever committed.** A push publishes history, not just the tree. Scan with a real regex engine over `git cat-file` blobs:

   ```sh
   git rev-list --objects --all   # sha + path for every object
   git cat-file blob <sha>        # feed to python re / ripgrep
   ```

   ⚠️ **Do not use `git grep -E` with `\b`** — POSIX ERE silently drops `\b`, matches nothing, and reports a false clean. This has happened; verify the scanner against a known-dirty line before trusting a clean result.

## Pattern classes

- **Names** (from the wordlist): clients, projects, project codes, counterparties, vendors, products, people, sites, substations, landowners.
- **PII and document artifacts**: emails, phone numbers, street addresses, DocuSign/envelope IDs, GPS coordinates, parcel/APN numbers.
- **Commercial figures**: dollar amounts, prices, caps, LD rates, milestone payments — any figure precise enough to have come from one contract.
- **Exact contractual values**: capacities to the unit digit, guarantee percentages, retention-table rows, loss allowances, thresholds, unit counts.
- **Executed dates**: signature, effective, delivery, COD dates from real deals.
- **Second-order fingerprints**: distinctive contract-window combinations (a specific notice period + test duration + cure window together can identify a deal even with every name stripped).
- **Quoted worked examples** — the most common leak class, because it hides in *your own generic prose*: a lesson ported from a project that still carries the real number, name, or typo that taught it. **Restate examples as patterns, never quotes** ("an LD cap with a truncated final digit", not the actual misprinted figure).

## What is allowed

- The maintainer's own brand and license line.
- **Multi-market examples with adapt-to-your-market caveats** (e.g. "CAISO/ERCOT/PJM-style signal names — adapt to the project's market"). Naming markets generically identifies no client; naming *one* market as an unstated assumption does — add the caveat or remove.
- Industry-generic terminology and standards bodies (NERC, GADS, OATT, Incoterms).
- When unsure: a term that would let a reader guess *which deal* taught the lesson gets removed or restated; a term any practitioner would use on any project stays.

## Findings handling

- **Working-tree hits**: fix in place (restate as pattern, genericize, or delete), then re-run to verify clean.
- **History-only hits**: report, don't auto-fix. Assess: is the commit already pushed (already public)? Purging requires a history rewrite + force-push, which breaks clones — that trade-off is the **user's call**; state the finding, its exposure (what exactly, how sensitive, how long public), and a recommendation.
- Record the audit (scope, wordlist date, findings, dispositions) in `log.md` — and in the PR description when run pre-PR.

## Close-out checklist

- [ ] Wordlist exists and is current for every project the contributor has touched
- [ ] Working tree scanned — all committable files, all types, including `log.md`
- [ ] Full history blob scan run with a verified regex engine (test against a known-dirty string first)
- [ ] All pattern classes covered, not just names
- [ ] Worked examples read specifically for quoted-not-restated material
- [ ] Findings fixed (tree) or dispositioned with the user (history); audit recorded in `log.md`
