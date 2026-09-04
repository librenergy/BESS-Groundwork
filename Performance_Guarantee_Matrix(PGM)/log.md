# Log

## 2026-09-03 (references moved to BESS_Experts)

* **`references/` moved to the top-level [`BESS_Experts/`](../BESS_Experts/index.md)** so the training material can grow independently of this document. The index and the main document's closing note now point there; nothing else in the PGM changed.

## 2026-09-02 (separator sweep: PG-xxx → PG_xxx)

* **Guarantee IDs now `PG_xxx`.** `PG-xxx` → `PG_xxx` across every non-log Markdown file (13 files, 102 code occurrences): the performance-guarantee series now uses the same underscore separator as the owner-metric series (`MT_BESS_001`), on the maintainer's decision to standardize. Numbers unchanged, so no metric was renumbered; only the separator moved. Log files keep the old form as history. The Mermaid.ai copy of the metrics tree re-pushed with the new labels. The header's ID rule and every row reference in this document and its calc sheets updated in place.

## 2026-09-02 (calc-sheet template: PG code in title and H1)

* `calc-sheet-template.md` title/H1 changed from "Calc Sheet: {{CONTRACT}} — {{METRIC}}" to "PG-{{ID}} — {{CONTRACT}} {{METRIC}}", and the rename instruction now says to carry the code in the frontmatter title and H1 as well as the filename — every appearance of a metric carries its reference. Ported from the first project clone, where a QC pass caught sheets whose files were renumbered but whose titles were not.

## 2026-09-02 (§6: shadow-calc definition home + Metric (ref) column)

* **§6 Shadow Calculation Requirements clarified as a data-requirements inventory, not a definition home** (callout added: a shadow calculation is an owner metric whose authoritative definition lives in the Metrics Tree under an `MT_` code), **and the table gained a "Metric (ref)" column**: the per-row pointer to that metric, ❓ until each project assigns codes at build time, — for raw inputs/externals/records that are not metrics. Both ported from the first project clone, where the column ships part-filled (`MT_BESS_001/002`).

## 2026-09-01 (calc-sheet data section is direction-aware)

* **`calc-sheet-template.md`'s "Data and metrics required to shadow it" section retitled "…to deliver / shadow it"** with a direction rule, ported from the first project clone: use **deliver** when this party is the contractual reporting party (typically offtaker-facing guarantees — the deemed-acceptance clock only helps if the number survives scrutiny), **shadow** when the counterparty reports and you verify (typically vendor-held guarantees). The clone's tolling sheets had been drafted in shadow language although the owner is the reporting party there.

## 2026-09-01 (calc-sheet filenames carry the PG code)

* **Calc-sheet naming convention changed from `calc-{contract}-{metric}.md` to `PG-{ID}-{contract}-{metric}.md`**, ported from the first project clone (its seven populated sheets renamed the same day): the guarantee's PG code leads, so the folder sorts by counterparty series and a sheet's filename alone identifies which matrix row it defines — matching the owner-metric pattern (`MT_<SEG_TYPE>_<NNN>-<slug>.md`) in the Metrics Tree. Updated: `calculations/index.md`, `calc-sheet-template.md` (which keeps its own name — it is the template, not a sheet), and the `performance-guarantee-matrix` skill (both mentions).

## 2026-08-31 (series-based guarantee IDs)

* **Guarantee IDs renumbered from sequential PG-01…PG-11 to counterparty series**, ported from the first project clone where sequential numbering hit its wall (an insertion shifts every later ID): **PG-1xx** offtaker, **PG-2xx** LTSA/supply provider, **PG-3xx** O&M provider; three digits, append-only within a series, series by counterparty not instrument. Mapping: 01→101, 02→102, 03→103, 04→104 (offtaker); 05→201, 06→202, 07→203, 09→204, 10→205, 11→206 (vendor side, incl. the commissioning, retention-warranty, and response-gap rows); 08→301 (O&M). Scheme stated in the matrix header. Swept: this matrix, the Metrics Tree (prose, node table, and diagram labels), and the Daily Performance Report; `log.md` files excluded per the never-sweep-logs rule.

## 2026-08-25 (later, tied to the metrics tree)

* **§1.3 added: cross-reference to the metrics tree.** States the division plainly, since the two documents had been drifting: **the [metrics tree](/Metrics_Tree%28MT%29/metrics-tree.md) is the source of truth for what gets monitored, this matrix for what the contract says.** Carries a node-to-row mapping table, and, more usefully, a second table listing the five rows **deliberately not on the tree** with the reason for each: PG-07 vendor RTE (a real claim, but the tree scopes the vendor side to power and capacity for readability), PG-08 O&M SLAs (service obligations, not a computed measurement), PG-09 commissioning (one-time, sets the denominators then stops mattering), PG-10 retention warranty (a defect path whose measurement the tree already carries), PG-11 response guarantee (no tree node because it usually has no contract). Closes with the maintenance rule: a new tree node with no row here is an unmapped mechanism, and a row whose tree node was deleted is a monitoring gap.

* **§5 reframed around the central outage log.** The exclusions table was a comparison of verdicts without ever saying what they were verdicts *on*. It now opens by naming the shared record: one event, logged once, GADS-aligned per the [Outage Tracker](/Data_Product%28DP%29/Outage_Tracker/outage-tracker.md), then ruled on independently by each contract, with this table as where the rulings are compared.

* Coverage verified mechanically in both directions: every guarantee ID on the tree resolves to a row here, and every row not on the tree is explained in §1.3.


## 2026-08-25

* **Matrix pre-filled from the OBE availability framework.** The template previously shipped as an empty grid: every seed row had a blank guaranteed level, blank assessment basis, and a blank exclusions table. A project starting from it had a structure but no position. It now carries a defensible starting point wherever the answer is engineering rather than commercial, and stays deliberately blank wherever it is commercial.

  **The dividing line, stated in §1.2:** methodology is pre-filled because it is identical on every project; **levels, LD rates, caps and evaluation periods are not**, because they come from the executed agreement and nowhere else. Inventing a "market" guaranteed percentage would be wrong more often than right and would read as authoritative.

  **§1.1 added: "availability" is four different measures, and a contract usually guarantees one.** Before any row can be filled, resolve which of EA / OA / PA / QA the instrument actually guarantees, with a table of what guaranteeing only that one leaves uncovered. Most agreements guarantee one type while calling it "availability" unqualified, and that ambiguity is where availability disputes originate.

  **§1.2 added: the reference position.** Default methodology per metric family, drawn from the framework: the rail-aware `min(ACP, ADP)` power availability with the weakest-string bus roll-up; equipment availability as `min(contactor, BMS status)` then `min(battery side, conversion side)` with data gaps defaulting to unavailable; operational availability with the ramp-widened dead-band and **directional** SOC exclusion; `QA = min(Q_test / Q_guaranteed, 100%)` as a scalar between capacity tests; SOC-matched RTE reported beside the raw figure. Plus three conventions that apply to every sheet regardless of family: excused capacity belongs in the **denominator**, losses are **directional**, and the owner must hold its own copy of every signal a guarantee is computed from.

  **Guarantee tables pre-filled** with assessment bases, evaluation-period expectations, and calc-sheet filenames, with `❓ per agreement` markers where the commercial term belongs. **PG-11 seeded as a deliberate gap row**: service agreements typically cover the site controller, so the provider owns the plant's ability to act on an instruction, yet almost none measure it. The row stays in with its level marked absent so the uncovered exposure is visible in the matrix and there is something concrete to table at the next amendment.

  **§5 exclusions gained an "expected pattern" column**, turning a blank grid into a checklist of what to go looking for, with the two largest retained exposures flagged: a BOP forced outage counts against the owner offtake-side while being excused vendor-side, and a controller command timeout scores opposite ways under the two contracts. A row that comes back different from the pattern is a finding to escalate, not a clerical correction.

  **§6 shadow requirements shipped populated** rather than blank, since the signal list any BESS availability calculation needs is knowable in advance: contactor and BMS status per string, PCS module status, string and module ACP/ADP, SOC in MWh with declared basis, per-string and per-bus SOC for imbalance attribution, setpoint as received against POI actuals, controller fault flags, capacity test results and the guarantee schedule, excused events from the Outage Tracker, POI normalisation constants, and the derate signals. Carries the standing warning that the capture must exist before the first operating year closes because none of it can be reconstructed later.

  **§4 gained the precedence rule**: start every sheet from the reference position, then overwrite it with the contract. Where the contract defines a method the contract governs and the reference becomes the shadow run beside it; where the contract is silent the reference *is* the method and gets cited so the choice is reviewable rather than buried in code. Either way the sheet reports both numbers, because the gap is the finding.

* **§1.2 gained the negotiation content moved out of the Daily Performance Report.** The daily report had been carrying the arguments for the power availability position, the terms-to-settle-in-the-clause table, and the fleet-size effect, all of which are guarantee-negotiation content rather than reporting content. Consolidated here so there is one source of truth: four arguments for the rail-aware minimum (sees one-sided derates, rail-awareness removes the only legitimate objection to a plain minimum, rolls up through real topology, asks the provider to instrument nothing new), the five terms worth more than the guaranteed percentage itself, and the fleet-size effect that makes a site-level power guarantee largely a guarantee against site-level failure.

* **Drafting-QC pass** on the whole document: 12 em dashes to 0, following the convention adopted repo-wide.
