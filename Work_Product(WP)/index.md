# Work Products

Engineering studies and reviews that the performance-engineering team **produces for the project** — point-in-time analyses that end in findings and recommendations. Most are run once during onboarding, then revisited on a review cycle or when something triggers them (an augmentation, a contract amendment, a significant event).

Distinct from the two neighbouring categories:

| | What it is | Cadence | Who executes it |
|:---|:---|:---|:---|
| **Work Product (WP)** | An engineering study or review — analysis ending in findings | Once, then on a review cycle or trigger | Us |
| [Data Product (DP)](../Data_Product%28DP%29/index.md) | A deliverable generated from documents + plant data | Recurring on a fixed cadence | A pipeline |
| [SOP](../Standard_Operating_Procedures%28SOP%29/index.md) | A step-by-step procedure issued to a counterparty | Standing, until revised | Someone else |

A study that concludes "the ROCC should do X when Y happens" produces an **SOP** as its output. A study that concludes "this number should be tracked monthly" produces a **data product**. The study itself stays here.

## Structure

One `.md` per work product while it is a single document; give it its own subfolder once it acquires companions (source data, extracts, an issued PDF) — the same rule `Project_Documentation/` uses. Subfolders are plain-named, no acronym (acronym suffixes are top-level only, [taxonomy §2b](../Definitions_Taxonomy%28DT%29/taxonomy.md)).

Each work product states up front: what triggered it, which documents it consumes, what it concludes, and what it hands off.

## Templates

* [IT Systems Reliability Study](IT_Systems_Reliability_Study/it-systems-reliability-study.md) - Site IT, network, and communications infrastructure walked from the ISP demarcation inward: every device tested for redundancy, spares, config recoverability, and who can rebuild it. Annual half-day exercise; feeds the spares list, the SOP set, and the failover test. *Template v0.1 — expected to harden with first real use.*

## The recurring set

The studies that come up on essentially every project. Only the IT systems study has a template so far.

* **Reliability study** - Failure modes, redundancy, and repair logistics resolved into an availability model: what the plant is actually expected to deliver versus what the availability guarantee assumes. Consumes the EIM (topology), the Data Interface Register (what is observable), and the Performance Guarantee Matrix (the number being defended). The anchor study — the spares review and the failover test both fall out of it. The IT systems study above is the communications-infrastructure counterpart; this one covers the power train.
* **Spare parts review** - Criticality ranking against lead times and stocking levels, tested against the repair windows the availability guarantee implies and the spares obligations in the LTSA/O&M contracts. The question it answers: which single part, out of stock, costs the most availability.
* **Failover / redundancy test study** - Proving the redundancy the reliability study assumed is real — comms paths, EMS/SCADA/PPC failover, ISO telemetry continuity, control handover. Written as a test plan with pass criteria, executed with the operating parties, and it usually produces SOPs.
* **Telemetry & data readiness assessment** - Onboarding gap audit: what the Data Interface Register says should exist versus what actually arrives, at what quality. Gates every metric downstream.
* **Performance test review** - Independent review or witnessing of capacity and RTE acceptance tests against the contract's stated method — the first place a shadow calculation gets exercised for real.
* **Augmentation & degradation review** - Capacity-maintenance strategy against the capacity guarantee and the warranty's cycle and temperature conditions.
* **Contract compliance / LD exposure review** - Where the project currently stands against each guarantee, and what the exposure is if the trend holds.
* **Root cause analysis** - Per significant event. Feeds the Outage Tracker event record, and often a warranty claim or an SOP revision.

Prune this list to what the project actually needs, and add what it turns out to need instead.

## Status

* **Category scaffold with one worked template.** The recurring set above is a catalogue, not a commitment — prune to what the project needs.
