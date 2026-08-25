# Log

## 2026-08-25 (draft-copy callout)

* **Draft online copy callout added, and the authority inverted.** The document previously said the cloud Mermaid editor was "the single working copy while drafting" and the repo block a synced snapshot. That is backwards for a public template: a subscription-gated link cannot be the record for a repo other people clone, and the lost-update incident earlier today showed the practical cost. **The diagram in the repo file is now stated as the source of truth**, with the Mermaid.ai link presented as a convenience that is easier to read and pan.

* **Public view links adopted.** The maintainer published both diagrams, so the callout now carries the short `mermaid.ai/d/<id>` view URL instead of a workspace edit URL. The "it is private" caveat was dropped since it no longer applies; the two that still hold are kept: **it drifts** (re-sync after editing online, re-fetch before editing online) and **access is not guaranteed** (publishing depends on a subscription that may lapse, which is why the inline block remains the record). The clone instruction stays: replace the link with your own diagram, or delete the callout.
* **Callout reframed after the access question: the URL form is not the problem.** Dropping `/edit` does not make a cloud diagram viewable, because it lives in one account's workspace and is private whatever the URL looks like. An unauthenticated fetch was inconclusive (the page is a JS app and returned only its `<title>`, which does confirm the diagram *name* is exposed). The callout is therefore written as a **convention** rather than a fixed link: it explains the cloud-editor pattern and its three caveats (it drifts, it is private, access is not guaranteed), then carries the maintainer's own draft link on a clearly-marked line noting it needs workspace access, with an instruction for anyone working in a clone to swap in their own link or delete the line. That keeps it useful now without a public template implying that a private workspace URL is a resource its readers can open.
* The callout carries the two caveats that matter: **it drifts** (edit either copy and they diverge; re-sync after editing online, re-fetch before editing online, because concurrent edits overwrite rather than merge) and **access is not guaranteed** (the link depends on a subscription that may lapse, so nothing in the repo may depend on it).


## 2026-08-25 (v1.0, tied to the PGM)

* **PG-NN references restored to the diagram**, at the maintainer's direction, on the reasoning that tying the two documents together is worth the small amount of label noise. The tree carries the ID; the PGM carries the level, rate, clock and calc sheet.

* **The relationship is now stated rather than assumed: this tree is the source of truth for what gets monitored, the PGM is the source of truth for what the contract says.** Every mechanism drawn here must have a PGM row. The reverse does not hold, and that asymmetry is deliberate.

* Verified both directions mechanically. All six IDs on the tree (PG-01 to PG-06) resolve to PGM rows. Five PGM rows are not on the tree (PG-07 vendor RTE, PG-08 O&M SLAs, PG-09 commissioning, PG-10 retention warranty, PG-11 the response gap row), and each now carries a stated reason in the new PGM §1.3 so the absence reads as a decision rather than an oversight.


## 2026-08-25 (v0.9, guarantee IDs off the diagram)

* **PG-NN identifiers removed from the node labels**, per the maintainer. The diagram now names the **mechanism** (availability LD, dispatch adjustment, power availability guarantee) rather than a template-assigned number, which reads better and avoids implying a numbering scheme a project has not adopted yet. Fixed the empty `<br>` tags the removal left behind on five nodes.

* **The cross-reference moved rather than disappeared.** The node-definition table's Metric ref column now names the guarantee in words, and step 3 of "how to use it" was rewritten: name the guarantees as the project's contracts name them, because levels, rates, clocks **and the guarantee IDs** live in the [PGM](/Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md). ⚠️ **The PGM still uses PG-01 to PG-11 as its own row identifiers and in its calc-sheet filenames**, which is the right place for them: a claim or a calc sheet needs a stable ID, a diagram does not. If the intent was to retire the numbering scheme entirely rather than just take it off the diagram, the PGM and its `calculations/` filenames are the other half of that change.

* **Central log wording adopted and extended.** Kept the maintainer's "(similar to Solar GADS)" framing and typo-fixed "scheduled maintenance". Added the reasoning to the prose: no standalone BESS GADS exists yet, storage appears today only as co-located groups in GADS Solar, and building the log GADS-aligned from day one means it exports rather than gets rebuilt when NERC opens BESS reporting. That matches the [Outage Tracker](/Data_Product%28DP%29/Outage_Tracker/outage-tracker.md)'s own premise.

## 2026-08-25 (v0.8, central outage log)

* **The outage log pulled out of the offtake box and made central.** It had been sitting under the availability LD as though it were an offtake artefact. It is not: it is the record of **every** event, every outage card raised with the grid operator, every trip, every derate, every piece of major downtime, and both agreements read it. It now sits outside both boxes, styled distinctly, owned by neither.

* **Two edges into it, carrying the two questions asked of the same event**: *excused under the offtake?* and *an excuse event for the vendor?* This is the "one event, judged twice" point that the toolkit states in prose in the daily report and the PGM, now drawn. The verdicts routinely disagree, and the classic case (a comms or BOP outage counting against the owner while being excused for the vendor) is exactly the gap the central log exists to make visible. Notice discipline lives here too, since a planned outage without timely notice usually converts to forced: a money event created by paperwork rather than by the plant.

* **Sections labelled by instrument**, per the maintainer: `OFFTAKE — PPA / Tolling Agreement` and `VENDOR — Long-Term Service Agreement (LTSA)`, each keeping its one-line purpose as a second line.

* Document updated to match: new "central outage log" subsection explaining the two verdicts and pointing at the [Outage Tracker](/Data_Product%28DP%29/Outage_Tracker/outage-tracker.md) for the event taxonomy and per-contract verdict fields, node-table row reclassified from an offtake signal to a shared central record, and a `Central log` class added to the taxonomy. Maintainer's own edits kept, including the capacity-test cadence note ("once or twice a year"), which is a useful reminder that QA is a scalar between tests.

## 2026-08-25 (v0.7, data sources removed)

* **The tree stops at the KPI level.** The maintainer removed the source-system nodes, on the grounds that where a number comes from is not what this diagram is for: the tree exists to identify **everything that affects revenue** so each box can be monitored. Systems, tags and interfaces are a later question, answered in the [Data Interface Register](/Data_Interface_Register%28DIR%29/data-interface-register.md) and in the metric sheets, and carrying them here only crowded out the thing the tree is for.

* Document reconciled to match: purpose statement rewritten around "everything that affects revenue", the "prioritising instrumentation" use case replaced with "setting the monitoring scope" (every box is something to watch; a box with nothing behind it is the gap), the reading guide and node taxonomy stripped of the source-node class, the four source rows removed from the node table, and the frontmatter description updated. The metric-definition sheet still carries **Authoritative data source** as a field, which is the right home for it: sourcing is a per-metric question, not a tree-shaped one.

* Net shape after a day of iteration: 28 nodes, two agreement boxes, four availability measures, one level of detail beneath each. Down from 59 at the widest point.

## 2026-08-25 (v0.6, grouped by agreement)

* **Grouped into two boxes, one per contract.** The tree is now organised by **the agreement that governs the money**, because that boundary is what decides which formula applies, which clock it runs on, and who you argue with. **Offtake agreement**: the payment in, the deductions off it (availability, dispatch, capacity and RTE), and the outage log with its notice tagging, which sits here because notice discipline is what decides whether an outage is excused. **LTSA provider**: the guarantees, the OBE measurement behind them, and the claim mechanism.

* **The three edges that cross between the boxes are the point of the diagram**: availability LD into equipment availability, dispatch adjustment into operational availability, and the capacity adjustment into the capacity test. The offtake side uses **the same nodes**, not copies, so one measurement serves both agreements even though the two contracts calculate, weight and excuse it completely differently. That is the argument for measuring the plant once, properly, rather than once per contract, drawn rather than asserted.

* **Source systems moved outside both boxes**, since a meter or a BMS does not belong to a contract.

* **Maintainer edits adopted**: the payments node (market or tolling payment, previously missing from the money walk), the renames to "LTSA and Vendor LD Recovery" and "Offtake deductions or LD", the Aqua/Rose/Pine colour classes, and the `TB` direction with `&` shorthand. Typo fixed in the root node ("Perfroamcance" to "Performance"). **The usage-limits node was removed by the maintainer and has been left out**, along with its prose and node-table row; the guarantee-termination risk it represented is now carried only by the PGM, which is a deliberate scope call rather than an oversight.

* Node table reconciled: payments row added, removed rows for the deleted usage-limits and invoice nodes, taxonomy updated for the two agreement boxes.

## 2026-08-25 (v0.5, detail restored under the measures)

* **One level of detail put back under each availability type.** v0.4 cut too deep: EA, OA, PA and QA went straight to source systems, which made them unactionable. A miss told you *which* measure was down but nothing about where to look. Restored: EA now breaks into battery and string faults, PCS faults, and BOP and grid; PA into charge and discharge power (ACP/ADP per string and module) and the derate causes (imbalance, temperature, voltage, current limit); OA into setpoint vs actual shortfall; QA into the capacity test. Seven nodes, 25 to 31, and the tree is still one screen.

* That detail is what makes the tree usable for root-cause: from a claim, down to the measure, down to the fault class, down to the system that reports it. Node table extended to match and the stale `CAPTEST` row removed (capacity testing is now the single `QTEST` node shared by the capacity guarantee and the offtake adjustment).

## 2026-08-25 (v0.4, cut back)

* **Cut from 59 nodes to 25, on one rule: if it does not change a number in the money walk, it is not on the tree.** The v0.3 tree had drifted into a map of everything a performance engineer touches. Condition-monitoring nodes (operating temperature envelope, PM completion on schedule, voltage window, owner-side duties, telemetry validity) were the clearest symptom: real work, but not a line of money, and they made the diagram unreadable. They now live where they belong, in the PGM exclusions and the SOPs.

* **Led with recovery, because it is the only money that comes back.** The root is now just two things: **what we claim back** (vendor LD recovery) and **what we lose** (offtake deductions). The recovery head comes first and gets the depth, since offtake deductions can only ever reduce a payment while the vendor claim is the one stream that pays money *in*.

* **The recovery side reads in build order**: the guarantees (a **power availability guarantee** and a **capacity guarantee**), then how each is measured (OBE for power availability, energy availability and its test for capacity), then how measurement becomes money (their number, our number, the difference with evidence, the claim and invoice). The middle step is the one projects skip, and skipping it means there is no difference to show and therefore no claim.

* **Kept exactly one condition: usage limits** (cycles, throughput, cumulative cut-off), drawn amber with a dashed edge into the recovery head. It earns its place because it is the only condition that can take the entire recovery limb to zero. Every other owner-side duty was cut.

* **Also dropped:** the operating-costs limb (not performance engineering, and it invited the tree to become a financial model), the separate RTE guarantee and shared-caps nodes (folded into the guarantee rows), and the whole guarantee-preservation limb apart from usage limits. Node-definition table rebuilt to 21 rows with no stale entries; supporting prose rewritten to match.

## 2026-08-25 (v0.3)

* **OBE moved under the vendor power availability guarantee.** In v0.2 the OBE block sat between the two contract limbs as a shared layer that both resolved into. That was the wrong emphasis: the four availability types are the machinery behind the **vendor** guarantee, which is the number that actually gets argued, and hanging them below both sides made the offtake limb look heavier than it is. OBE now hangs off PG-05, where the claim is made.

* **The two sides are measured to different depths, deliberately.** The offtake side uses **two** measures, which is enough to explain almost every deduction on that limb: **EA** (was the equipment on?) for the availability LD, and **OA** (did we follow the setpoint?) for the dispatch adjustment. The vendor side uses **all four**, because the power availability guarantee is where the OEM's definition gets argued against ours. EA and OA are drawn once and shared, so the same two measures serve both sides and the vendor side adds the two the offtake side never asks for.

* **Plain language throughout.** Node labels rewritten as questions and short phrases: "is the equipment on?", "did we follow the setpoint?", "what we claim back from the OEM", "break these and we lose the right to claim", "taper is imbalance, not wear". The diagram should be readable by someone who has not read the documents.

* **⚠️ Lost update on the cloud copy, and what it cost.** A v0.3 push was overwritten by a concurrent save from the maintainer's open editor tab: a re-fetch afterwards returned the **v0.2** structure with a `layout: elk` config added and the `%%` comments stripped. The elk layout was kept (it lays out a wide graph far better than the default) and v0.3 was re-pushed on top. This is the exact failure the EIM convention warns about, and it now has a worked example: **re-fetch before every edit, and refresh the editor tab after any agent-side push**, because these edits do not merge. The `%%` comment block did not survive; the edge and owner semantics now live only in this document's prose, which is arguably the better home anyway.

## 2026-08-25

* **Tree rebuilt to v0.2: from a generic merchant shape to a performance-engineering shape.** The old tree had two limbs, Commercial Value and Asset Health, decomposing market revenue (arbitrage, ancillary services, capacity) into a health index. It described a merchant asset and gave a performance engineer nothing to monitor: no LDs, no guarantees, no preservation conditions, and no route from a headline miss to the signal responsible. Structure adapted from the shape that emerged on the first project clone (structure only, no project content ported), then extended with everything the toolkit has learned since.

  **Four limbs now**, each terminating in something a PE measures, reconciles, or defends: **Contract Revenue** (base payment minus the LD and adjustment deductions, drilled through the contractual calculation into the outage log and its attribution), **Vendor LD Recovery** (the claim tree, with the vendor's self-assessment as the primary number and the owner's shadow replica beside it), **Operating Costs** (deliberately shallow, outside PE scope, because decomposing it invites the tree to become a financial model), and **Guarantee Preservation** (the gate: usage limits, envelopes, PM, telemetry validity, whose breach forfeits recovery under limb 2).

  **The structural claim is the new OBE layer.** The teal node and its four children sit **below both contract limbs and above the equipment**: the offtake availability number and the vendor availability number are different constructs written by different parties, but they measure the same plant, and that plant is measured once, correctly, by EA / OA / PA / QA. Both contractual calculations resolve downward into it. The two contractual streams are drawn with **no link between them**, since they use different formulas, boundaries, clocks and LD pools and settle separately; linking them would invite exactly the reconciliation error the toolkit warns against.

  **New node class: `gap` (amber dashed)**, used for PG-11, the response guarantee that service agreements almost never carry. Drawing an absent guarantee is deliberate: it keeps the uncovered exposure visible on the map rather than silently missing from it.

  Guardrails rewritten around real cross-contract mechanics: a comms failure counting against the owner while excused for the vendor, telemetry invalidity shrinking the vendor's denominator, out-of-envelope operation becoming an excuse event, a one-sided derate being invisible under a `max()` definition, and the EFC counter running toward guarantee termination.

  Supporting sections rewritten to match: node-definition table repopulated for the new IDs (32 seeded rows, no stale entries), starter metric set now leads with the four OBE measures, source-of-truth table gained ACP/ADP and energy capacity rows, reconciliation rules gained the **reconcile the vendor side monthly, not annually** rule (an annual-only check discovers disputes after the evidence window closes), and customization notes now explain how a merchant asset swaps limb 1 while keeping limbs 2 to 4 and the OBE layer unchanged, because the plant is the same plant.

* **Cloud working copy updated.** The Mermaid.ai diagram (`BESS-Groundwork Metric Tree`) was fetched, replaced with the v0.2 tree, and validated before pushing; the block in this document is the synced snapshot. Convention recorded in the header: the cloud copy is the single working copy while drafting, re-fetch before editing, refresh the editor tab after an agent-side push.

* **Genericity and drafting QC.** No project, counterparty, OEM or figure from any clone: all guarantee levels, LD rates and capacities are `❓` or PG-NN pointers into the PGM. Prose em dashes taken to zero; the 24 remaining are standalone table placeholders, which the drafting-QC skill exempts, and the diagram's own labels follow the existing convention for Mermaid source.
