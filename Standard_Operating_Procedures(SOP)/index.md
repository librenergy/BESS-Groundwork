# Standard Operating Procedures

Step-by-step procedures that **someone else executes** — the ROCC operator at 2 AM, the O&M technician on site, the GOP dispatcher, the scheduling coordinator. These are the documents that leave the repo: they get issued to operating parties and counterparties, who then work from them.

That external audience is what makes SOPs their own category rather than a kind of [work product](../Work_Product%28WP%29/index.md). A study is finished when it is delivered; an SOP is a **controlled document** with a life after issuance — it is revised, re-issued, acknowledged, and audited against.

## Boundary with the RACI — do not restate

Two documents describe who does what in an event, and they must not duplicate each other:

| Document | Answers |
|:---|:---|
| [RACI Matrix](../RACI_Matrix%28RACI%29/raci-matrix.md) | Who is accountable for this class of event, and (escalation sections) who calls whom, in what order, within what deadline |
| **SOP** | What the person actually does, step by step |

An SOP **references** the RACI's escalation sections for notification chains and the [Contact Register](../Contact_Register%28CR%29/contact-register.md) for the phone numbers — it never copies them, because a copied phone number is a wrong phone number eventually. When the escalation chains change, SOPs stay correct.

## Controlled-document discipline

Because these are issued to third parties, each SOP carries a header block: **owner, revision, effective date, review cadence, distribution list, and supersedes**. Two rules follow:

- **The repo holds the source of truth; the issued file is a snapshot.** Log every issuance in this folder's `log.md` — revision, date, and who it went to. When a dispute turns on whether a party was operating to the current procedure, that log is the evidence.
- **Acknowledgement is tracked, not assumed.** An SOP nobody confirmed receiving is not in force.

Several SOPs exist specifically to keep warranty coverage valid — the owner-side conditions in the [Warranty Obligation Matrix](../Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) (cycle limits, temperature envelopes, PM compliance, firmware control) are only met if somebody follows a procedure. Those SOPs should cite the clause they protect.

## Structure

One `.md` per procedure while it is a single document; its own subfolder once it acquires companions — the issued PDF, acknowledgement records, a counterparty's redline. Subfolders are plain-named, no acronym ([taxonomy §2b](../Definitions_Taxonomy%28DT%29/taxonomy.md)).

## The recurring set

Procedures that most projects end up needing. No procedure templates written yet.

* **Alarm response & triage** - What the ROCC does with each alarm class: acknowledge, assess, dispatch, or escalate. The most-used procedure on the list.
* **Outage reporting & ISO submission** - Recording an outage and getting it into the ISO's outage system inside the reporting window, with the classification that matches the [Outage Tracker](../Data_Product%28DP%29/Outage_Tracker/outage-tracker.md) event record. Misclassification here is what makes availability numbers disagree later.
* **Telemetry loss / data gap recovery** - Detection, interim reporting obligations, backfill, and how the gap is flagged in downstream metrics.
* **Curtailment & dispatch non-compliance handling** - What to do when the plant cannot follow the setpoint, and how the event gets characterised before it becomes a deduction.
* **Emergency response — thermal event / fire** - Site interface to the emergency plan, plus the notification obligations that carry contractual deadlines (insurer, offtaker, lender).
* **Switching & LOTO coordination** - The scope boundary between BOP O&M and the LTSA provider, which is where people get hurt if it is vague.
* **Firmware & configuration change control** - Approval and record-keeping for changes that can void warranty coverage or silently alter a measured value.
* **Seasonal readiness / extreme weather preparation** - Pre-event checklist and standby posture.
* **Meter data exchange & settlement submission** - The monthly data handoff, its deadlines, and what to do when a file is late or fails validation.
* **Site access & entry control** - Who may be on site, under whose escort, logged how.

Prune to what the project actually needs; add what the failover test and reliability study turn up.

## Status

* **Category scaffold — no procedure templates yet.** The set above is a catalogue, not a commitment.
