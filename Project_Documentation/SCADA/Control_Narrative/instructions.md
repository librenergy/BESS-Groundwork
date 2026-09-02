---
type: Guide
title: Control Narrative Review Instructions
description: How to review a plant control narrative from a performance-engineering perspective - the real power chain, the grid interface and its refresh rates, available-power telemetry, and the voltage chain down to the inverter terminals.
---

# Control Narrative Review Instructions

How to review a **plant control narrative** (the document describing what the plant controller does) for a BESS, from a performance-engineering perspective.

The mechanical workflow (place, extract, verify, index, propagate) lives in the [`project-document-review`](/.agents/skills/project-document-review/SKILL.md) skill. The SCADA folder's `instructions.md` covers drawing sets, points lists and HMI captures. This file covers control logic, and it deliberately reads a control narrative **differently from how a controls engineer would**.

## The lens

A controls engineer reads this document to confirm the plant meets its interconnection obligations. We are not primarily doing that. Compliance items (ramp limits, PFR droop, PF envelope, voltage schedule) matter here only as a **check against the grid agreement**, recorded and moved past.

What we care about is the chain that determines **how many megawatts leave the plant, on whose instruction, measured where, and reported by whom**. That chain is what every guarantee, LD and settlement dispute eventually turns on. Concretely, we are reading for:

1. **The real power chain.** Where the MW setpoint comes from, what clamps it, what the PID controls against, and how the resulting command reaches the units. This is the heart of the review.
2. **The grid interface.** Which operating modes the plant can be placed in from outside, what the offtaker or grid operator can send, and what the plant does with each.
3. **Schedules and refresh rates.** Dispatch schedules, real-time setpoints, update intervals, staleness handling and timeouts. A control document that names no refresh rate has not specified the interface.
4. **Available-power telemetry.** Whether available charge and discharge power appear at all, and whether the controller consumes them, publishes them, or both. Often absent, because it is reporting rather than control. Its absence is itself the finding.
5. **The voltage chain to the inverter terminals.** Not grid voltage compliance, but the actuators between the POI and the battery-unit terminals, and whether anything holds the terminals inside the OEM's guarantee window.
6. **Energy state.** Whether stored energy participates in control at all.

Ordering principle: **follow the megawatt, then follow the volt, then ask what the controller knows about energy.**

## Before starting

Read, in this order:

- The folder [definitions.md](definitions.md) if one exists, then the SCADA folder's and the global glossary.
- The **single-line diagram** extraction. You cannot review a voltage chain without knowing what actuators exist. Transformer taps, regulator devices and bus arrangement come from the SLD, not from the narrative.
- The **OEM service agreement's** guarantee terms: measurement basis and operating conditions.
- The **grid agreement's** operating envelope.
- The **offtake agreement's** dispatch and telemetry obligations.

A control narrative reviewed in isolation yields a summary. Reviewed against those four, it yields findings.

## The passes

### Pass 1 — The real power chain (the core pass)

Trace the MW path end to end and write it as an ordered chain, one row per transformation. For each step record what acts, on what quantity, and with what authority to override the step before it.

Questions to answer explicitly:

- **Setpoint origin.** Every source, named. Operator entry, external dispatch, scheduler, default. What arbitrates between them?
- **Clamps, and what they clamp.** What limits the setpoint, in what order, and are the limits power, energy, or both? Watch for documents that name an energy figure (MWh) among power limits: that is a category error and usually means energy is not actually limited.
- **The export cap: setpoint limit or output limit?** Interconnection agreements typically oblige the *plant controller* to limit output to the interconnection rating. A clamp on the **setpoint** is not that. Establish which one the document describes, and then check the chain order: any term added **after** the clamp escapes it. Frequency response is the usual offender, because droop is applied to the rate-limited setpoint and can add tens of megawatts on an oversized plant. Compute the worst case from the droop coefficient and compare it to installed capability, not to the rating: if installed capability exceeds the rating, the excursion is physically deliverable. Then ask which of the three answers the project has chosen: exceed the cap to honour droop, clamp and forgo upward response, or reserve headroom by dispatching below the rating. All three are defensible; only one is usually in the grid agreement; and many narratives contain none of them. Watch also for an **assertion without an implementation**: a generic overview section claiming output is "always limited" while the site-specific section describes only a setpoint clamp.
- **The import cap.** Grid agreements often limit output and say nothing about import. Establish what caps charging and at what value.
- **PID bounds.** Gains, anti-windup and output rate limits. Without them, transient overshoot at the cap is bounded by nothing documented, and a PID whose output limits are expressed as percentages is not enforcing a megawatt cap.
- **The controlled variable.** What is the PID's process variable, and **where is it measured**? This is the single most important sentence in the document. A PID closing on a revenue meter at the POI behaves very differently from one closing on inverter output.
- **Loss compensation, and who pays for it.** If the PID controls to a POI or POM quantity, the controller automatically commands the units harder to overcome collector and transformation losses. Record this explicitly: it means the plant self-compensates losses up to the point where unit headroom runs out, and it interacts directly with any guarantee whose measurement basis is stated at a different point from the units.
- **Fault masking.** Most narratives contain a sentence like "the PID compensates for dips in production". For a BESS with installed capability above the contractual rating, that sentence means **unit unavailability is invisible at the meter until the headroom is exhausted**. Chase it: how much headroom, and what does it hide?
- **The command to the units.** Global or per-unit? Percentage or absolute? Signed or unsigned? Distributed evenly, pro rata, or by merit? What is the population it is spread across, and how is that population defined?
- **Signed range sanity check.** A BESS charges. Confirm the setpoint range, the PID output range, and the unit command range are all consistent with bidirectional operation. Solar-derived narratives routinely specify a 0 to 100% output on a plant that must also absorb.

Record the chain as a numbered table in the summary. It becomes the reference every later question is answered against.

### Pass 2 — The grid interface and operating modes

Inventory every way the plant can be commanded or constrained from outside the fence.

| Mode / input | Who sends it | Path and protocol | What the plant does | Refresh | On loss |
|---|---|---|---|---|---|

Cover at least: real power dispatch, reactive or voltage mode selection, curtailment, emergency or ramp-to-zero signals, automatic generation control, market or scheduling instructions, and any manual override. For each, ask whether the narrative describes the plant as **controlled** by the counterparty or merely **monitored** by them. That distinction decides who is responsible when a dispatch is missed, and it is very often left ambiguous.

Note also which modes are mutually exclusive, and what happens on a mode change mid-ramp.

### Pass 3 — Schedules, refresh rates and staleness

Frequently the weakest part of a control narrative, and disproportionately important for us.

- Can the plant receive a **schedule** (a forward set of setpoints with timestamps), or only an instantaneous setpoint? A schedule implies storage, validation, a clock and a fallback; an instantaneous setpoint implies a live link.
- **Refresh rate** of every external input, and the controller's own scan rate.
- **Staleness handling.** How long is a setpoint honoured after the last update? Is there a timeout, and what state does it fall to?
- **Time synchronisation.** What clock, what source, and is it the same clock the guarantee data is stamped with? Interval-boundary disagreements between the controller and the settlement meter are a recurring dispute source.
- **Communication loss behaviour, per input.** Expect asymmetry between loops, and question it: the same lost link should not produce "hold forever" on one loop and "fail off" on another without a stated reason.

Any input without a stated refresh rate is an open item, not a detail.

### Pass 4 — Available-power telemetry and the reporting boundary

Available charge power and available discharge power are usually **not** control quantities, so a control narrative may omit them. Check anyway, and record precisely which of these three the document supports:

1. The controller **receives** available power (typically from the OEM controller) and uses it as a clamp.
2. The controller **computes or publishes** available power outward.
3. Neither.

This matters because available power is the quantity the OEM availability guarantee is computed from, and because energy-constrained variants of it ("MW available for the next hour") are frequently owed to the offtaker or balancing authority. Establish where each is produced, and whether the owner can see it independently of the OEM.

Also check whether the narrative describes what the controller **records** for later reporting. Most do not, which means the availability evidence trail lives entirely in the historian and the OEM's own logs.

### Pass 5 — The voltage chain to the inverter terminals

Grid voltage compliance is a Pass 7 checklist item. This pass is about something else: **the voltage the OEM's guarantee is conditioned on**, which is at the inverter terminals, several transformations away from anything the narrative controls.

Build the chain from the SLD, then ask what the narrative says about each link:

| Link | Actuator | Control target | Who sets it | In the narrative? |
|---|---|---|---|---|
| POI / HV bus | Grid, plus plant reactive output | Grid operator's voltage schedule | Grid operator | |
| Main transformer taps | OLTC or fixed taps | Usually the MV bus | Regulator relay, or nobody | |
| MV collector bus | Bus voltage | | | |
| Unit transformers | Taps, usually fixed | | | |
| Inverter terminals | Reactive output per unit | The OEM guarantee window | | |

Then answer:

- **What actuators exist?** An on-load tap changer, a fixed tap, a regulator relay, per-unit reactive control. Get this from the SLD, not the narrative.
- **What is each actuator's control target, and who sets it?** A tap changer regulating an MV bus is the main lever over inverter-terminal voltage, and it is frequently controlled by a standalone regulator relay that the plant controller neither commands nor monitors.
- **Are there two uncoordinated voltage actuators?** Plant-level reactive control and a tap changer both move voltage, on different timescales, toward different targets. If the narrative describes one and is silent on the other, that is a design gap, not a documentation gap.
- **Is terminal voltage measured anywhere the owner can see?** If the OEM adjusts a guarantee quantity based on a voltage only the OEM measures, the owner cannot audit it.
- **What happens in the worst case?** Full-power charging plus reactive absorption plus a low grid voltage setpoint is the combination that drags terminals down. Confirm the design holds the window across the full P/Q envelope, in both directions, and name it as an acceptance test if unproven.

### Pass 6 — Energy state in control

- Does stored energy appear as an input to any control loop?
- Are the offtake agreement's state-of-charge bands enforced anywhere, and by what?
- Does the frequency-response commitment carry an energy check? A droop response promised without a state-of-charge arming range is a compliance exposure, not a performance one.
- Which system computes the energy-derived telemetry the contracts require, and does that value ever return to the controller?

Energy state computed for telemetry but absent from control is an architecture finding worth stating plainly.

### Pass 7 — Compliance envelope (record and move on)

Check against the grid agreement, record any mismatch as a finding with the clause reference, and do not elaborate:

- Ramp rate limits, including whether the controller clamps operator entry, and whether charging-direction ramp is addressed at all.
- Frequency response droop, deadband and the derived proportional response. Recompute the arithmetic.
- Power factor and reactive envelope.
- Voltage range and schedule-following obligation.
- Ride-through and protection interactions, including any hardwired ramp-to-zero scheme.

These are real obligations, but they are the interconnection engineer's brief. Our interest is that a mismatch here can produce a counterparty-ordered curtailment that looks grid-caused and is actually project-caused, which is an outage-classification problem.

### Pass 8 — Failure and degraded modes

For each loop and each external input: what is the failure detection, what is the fallback state, is there a timeout, does recovery need a human, and is anyone notified? Map each failure mode onto its outage-classification consequence.

### Pass 9 — Document integrity

- **Placeholders.** List every unfilled bracket. In a post-commissioning or post-FAT revision, an unfilled parameter is a live question about whether the function was configured at all.
- **Template residue.** Control narratives are heavily reused across projects and technologies. Solar language in a storage document ("clouds", "inverters", "production") is evidence about the design's provenance and predicts exactly which bidirectional behaviours will be missing.
- **Text against figures.** Read the figures as images, not extracted text. Confirm that the decision trees and PID blocks actually contain what the prose claims.
- **Internal contradictions.** Captions, ranges, and cross-references.

## Companion set

| File | Contents |
|---|---|
| `summary.md` | Document metadata, site data with cross-checks, **the real power chain table**, the grid interface table, the voltage chain table, control-loop parameters, failure modes, and the cross-document reconciliation table |
| `definitions.md` | Acronyms, loop names, setpoint-source flags, sign conventions, controller tag names, and terms the document uses without defining |
| `red-flags.md` | Findings ordered by consequence to the owner, each with the text as written, why it bites, and mitigations |
| `todo.md` | Questions for the integrator (as one package), placeholders, acceptance items before energization, cross-document propagation |
| `index.md` / `log.md` | Folder index and dated log entry |

No `telemetry.md`: communications architecture belongs to the SCADA folder, and control tags belong in `definitions.md`.

## Known traps

- **Reading the compliance sections and stopping.** They are the easiest part to check and the least valuable. The MW chain is where the money is.
- **Trusting extracted text for figures.** Decision trees and PID blocks carry the actual logic. Render the pages.
- **Accepting "the PID compensates for dips in production" as boilerplate.** On an oversized plant it is a statement about what the meter will not show you.
- **Reading a limit claim as a limit.** "Output is always limited to the site capacity" in an overview section is an assertion. The implementation is in the site-specific logic, or it is nowhere. Check the chain order rather than the sentence.
- **Assuming the narrative's device names are as-built.** Cross-check meter models and tags against the HMI captures and points list. A narrative naming a contractually specified meter is asserting compliance, not evidencing it.
- **Treating a missing refresh rate as a detail.** It is the difference between a specified interface and an assumed one.
- **Letting the OEM controller absorb every unanswered question.** "The OEM controller handles it" is a boundary, not an answer: what sits behind it is unauditable by the owner and usually appears in the guarantee as a self-assessment.
- **Confusing the plant controller's voltage loop with the transformer's.** They are different actuators with different targets and different owners.

## Cross-checks before closing

- **SLD:** actuators, taps, regulator devices, meter locations, protection and ramp-to-zero paths.
- **OEM service agreement:** measurement basis, operating conditions, the availability calculation's inputs.
- **Grid agreement:** operating envelope, telemetry and control obligations, ramp and voltage duties.
- **Offtake agreement:** dispatch mechanics, energy-state bands, telemetry owed.
- **Points list and HMI:** tag names, meter models, per-unit point set.
- **Telemetry map and interface register:** every external input and output in this narrative should already be a row, or become one.

## Wrap-up checklist

- [ ] Real power chain written as an ordered table, with the PID's process variable and its measurement point stated.
- [ ] Loss compensation and fault-masking behaviour explicitly recorded, with the headroom quantified.
- [ ] Signed-range consistency confirmed across setpoint, PID output and unit command.
- [ ] Grid interface table complete, with controlled-versus-monitored resolved for each input.
- [ ] Every external input has a refresh rate, a staleness rule and a loss behaviour, or an open item.
- [ ] Available charge and discharge power classified as received, published, both, or absent.
- [ ] Voltage chain table built from the SLD, with each actuator's target and owner named.
- [ ] Energy state's role in control stated, including the frequency-response arming question.
- [ ] Compliance envelope checked against the grid agreement, mismatches recorded with clause references.
- [ ] Failure modes mapped to outage-classification consequences.
- [ ] Placeholders and template residue listed.
- [ ] Figures verified as images.
- [ ] Cross-document conflicts recorded and propagated.
