---
type: Template
title: IT & Site Communications Reliability Study
description: "Component-by-component walk of the site's IT, network, and communications infrastructure — from the ISP demarcation inward — testing each failure point for redundancy, spares, config recoverability, and the named people who can rebuild it."
---

# IT & Site Communications Reliability Study

**Project:** `{{PROJECT_NAME}}`  **Site:** `{{SITE}}`  **Version:** `{{VERSION}}`  **Study date:** `{{DATE}}`
**Performed by:** `{{NAMES}}`  **Participants:** `{{IT/OT, GOP, LTSA, SCADA vendor}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Companion docs:** Data Interface Register, Contact Register, Warranty Obligation Matrix
**Next review due:** `{{DATE + 1yr}}`

> **What this is.** A walk through every device between the ISP demarcation and the last field controller, asking the same four questions of each one. It is deliberately shallow and wide — an annual half-day exercise, not a design review. The depth goes into the components that fail the questions.

## 0. Why this exists — three uses

1. **Annual health check.** Walk the list, update the answers, see what has drifted. Redundancy decays quietly: a failover path gets decommissioned, a spare gets consumed and not replaced, the one engineer who could reprogram the RTAC leaves.
2. **Input to spares and procedures.** Every "no spare" answer becomes a line in the [Spare Parts Review](../index.md); every "nobody knows how to recover this" becomes an [SOP](../../Standard_Operating_Procedures%28SOP%29/index.md). This study is where those two lists come from.
3. **Lookup during an outage.** When site comms drop at 2 AM, §8 answers *which component, was it supposed to be redundant, do we have a spare, and who do we call* — without anyone reconstructing it under pressure.

## 1. The four questions

Asked of every component in §4. A component is only "covered" if all four have answers.

| # | Question | Fails when |
|:--|:---|:---|
| **Q1** | **Is it redundant — and has the failover been tested?** | There is a second unit, but nobody has ever pulled the cable to confirm it takes over. Untested failover is not redundancy. |
| **Q2** | **Is there a spare, and where is it?** | "The vendor can ship one" — with a lead time longer than the outage the guarantee tolerates. |
| **Q3** | **Can it be rebuilt — config backup, tested restore?** | The hardware is replaceable in an hour and the configuration takes three weeks to reconstruct. This is the most commonly failed question. |
| **Q4** | **Who can reprogram it — named, reachable, equipped?** | One named person, one vendor-locked toolchain, no license, no credentials, or no source code. |

The failure order in practice is **power → configuration → people**, not hardware. Budget the study's attention accordingly.

## 2. Scope

**In scope:** everything carrying site data or control traffic — ISP circuit and edge equipment, firewalls and remote access, core and field networking, protocol gateways, control and telemetry devices (PPC, RTAC/RTU, relays, meters), SCADA and historian compute, the power feeding all of it, and the environment it sits in.

**Out of scope:** corporate IT, the battery/PCS equipment itself (that is the reliability study proper), and anything the utility owns beyond the interconnection demarcation — *except* the handoff points, which are in scope precisely because ownership boundaries are where recovery responsibility gets dropped.

**Boundaries to name explicitly:** `{{who owns the ISP contract}}` · `{{utility-owned vs project-owned at the POI}}` · `{{LTSA-scope vs BOP-scope network segments}}` · `{{which segments are under NERC CIP or other cyber regime, if any}}`

## 3. The path walk

Before the tables, draw the actual traffic paths — it is what makes single points of failure visible. Reference the [Entity Interaction Map](../../Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) for the external endpoints and the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md) for the interface IDs each path carries.

Trace each of these end to end and note **every device it traverses**:

| Path | From → To | Interface IDs (DIR) | Consequence if lost |
|:---|:---|:---|:---|
| ISO/utility telemetry | Plant → `{{ISO}}` | `{{IF-nn}}` | Dispatch non-compliance, telemetry-loss penalty |
| Dispatch / setpoint | `{{ISO or offtaker}}` → PPC | `{{IF-nn}}` | Cannot follow dispatch |
| Revenue metering | Meter → `{{SC / ISO}}` | `{{IF-nn}}` | Settlement estimated, not measured |
| Remote operations | ROCC → site | `{{IF-nn}}` | Blind and hands-off |
| Performance data | Site → historian/platform | `{{IF-nn}}` | Data gap → availability dispute |
| Vendor remote support | OEM/LTSA → equipment | `{{IF-nn}}` | Truck roll instead of remote fix |

> **The question this table exists to expose:** do two or more of these paths share one device, one circuit, or one ISP? A "redundant" ISO telemetry path riding the same modem as everything else is not redundant.

## 4. Component inventory

Two tables per zone. **Table A — Redundancy** answers Q1/Q2; **Table B — Recoverability** answers Q3/Q4. Keep one row per physical device; give each an ID (`IT-<ZONE>-nn`) and use it everywhere else in this document.

Status values: **R** redundant & tested · **R?** redundant, untested · **S** spare held · **G** gap · **SPOF** single point of failure with no spare.

*(Rows below are illustrative — replace with the real inventory.)*

### 4.1 WAN & external connectivity

Zone `WAN`: ISP circuit(s), demarcation, modem/ONT, edge router, cellular or satellite backup, static IP allocation.

**A — Redundancy**

| ID | Component | Make / model | Location | Redundant? | Failover auto/manual | Last failover test | Spare | Status |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-WAN-01 | ISP circuit A | `{{carrier, service}}` | Demarc | `{{2nd carrier?}}` | | | n/a | |
| IT-WAN-02 | ISP modem / ONT | | | | | | | |
| IT-WAN-03 | Edge router | | | | | | | |
| IT-WAN-04 | Cellular backup | | | | | | | |

**B — Recoverability**

| ID | Config backup location | Last backup | Restore tested | Who can reprogram | Their tooling / license | Source or config IP held by | Support contract & SLA |
|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-WAN-01 | n/a | n/a | n/a | `{{carrier}}` | | n/a | `{{SLA, hours}}` |
| IT-WAN-02 | | | | | | | |

**Zone-specific checks**

- [ ] Is the backup path **diverse** — different carrier, different physical route, different last mile? Two circuits in one trench is one circuit.
- [ ] Does the backup path have the **bandwidth and latency** the ISO telemetry actually requires, or is it only good for email?
- [ ] Has the backup path **carried production traffic** in the last 12 months, or is it dormant and assumed working?
- [ ] Are the **static IPs** documented, and is the ISO/utility endpoint whitelisted on *both* paths?
- [ ] Who is authorised to **open a ticket** with the carrier, and is that person reachable out of hours?

### 4.2 Security edge & remote access

Zone `SEC`: firewall(s), VPN concentrator, vendor remote-access path, jump host, DMZ, NAT/port forwarding.

**A — Redundancy**

| ID | Component | Make / model | Location | Redundant? | Failover auto/manual | Last failover test | Spare | Status |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-SEC-01 | Firewall | | | `{{HA pair?}}` | | | | |
| IT-SEC-02 | VPN / remote access | | | | | | | |
| IT-SEC-03 | Jump host | | | | | | | |

**B — Recoverability**

| ID | Config backup location | Last backup | Restore tested | Who can reprogram | Their tooling / license | Source or config IP held by | Support contract & SLA |
|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-SEC-01 | | | | | | | |

**Zone-specific checks**

- [ ] Does the firewall carry a **subscription or license** that expires? An expired license can fail the device closed.
- [ ] Are **rule sets** backed up and version-controlled, or does recovery mean rebuilding policy from memory?
- [ ] Is vendor/OEM remote access **individually accounted** — named accounts, MFA, revocable — or a shared credential nobody can rotate?
- [ ] Who **removes access** when a vendor's engineer leaves, and is anyone checking?
- [ ] Where do **credentials and certificates** live, and who else can reach that vault if the owner is unavailable?

### 4.3 Core & field network

Zone `NET`: core switch, distribution/access switches, fiber to enclosures and PCS blocks, media converters, patch panels, protocol gateways, wireless links.

**A — Redundancy**

| ID | Component | Make / model | Location | Redundant? | Failover auto/manual | Last failover test | Spare | Status |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-NET-01 | Core switch | | Control building | `{{stacked / MLAG?}}` | | | | |
| IT-NET-02 | Field switch, block `{{n}}` | | Enclosure | `{{ring topology?}}` | | | | |
| IT-NET-03 | Fiber run, control bldg → block `{{n}}` | | | `{{diverse path?}}` | | | | |
| IT-NET-04 | Protocol gateway (`{{Modbus/DNP3}}`) | | | | | | | |

**B — Recoverability**

| ID | Config backup location | Last backup | Restore tested | Who can reprogram | Their tooling / license | Source or config IP held by | Support contract & SLA |
|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-NET-01 | | | | | | | |

**Zone-specific checks**

- [ ] Is the field topology a **ring with a tested break**, or a star that pretends to be a ring?
- [ ] Are there **spare fiber strands and patch cords**, and are the runs documented well enough to find a break?
- [ ] Are **VLAN and IP allocations** documented outside the devices themselves?
- [ ] Do any switches carry **PoE-dependent devices** that die when the switch does — cameras, wireless APs, sensors?
- [ ] Are switch **firmware versions** recorded, and is any device end-of-support?
- [ ] For an industrial/managed switch: does the replacement need to be **the same model and firmware** to accept the saved config?

### 4.4 Control & telemetry devices

Zone `CTL`: utility/POI RTAC or RTU, plant controller (PPC), EMS controller, protective relays, revenue and check meters, ISO telemetry interface.

**A — Redundancy**

| ID | Component | Make / model | Location | Redundant? | Failover auto/manual | Last failover test | Spare | Status |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-CTL-01 | Utility / POI RTAC or RTU | | Substation | | | | | |
| IT-CTL-02 | Plant controller (PPC) | | | | | | | |
| IT-CTL-03 | EMS controller | | | | | | | |
| IT-CTL-04 | Revenue meter | | | `{{check meter?}}` | | | | |
| IT-CTL-05 | Protective relays | | | | | | | |

**B — Recoverability**

| ID | Config backup location | Last backup | Restore tested | Who can reprogram | Their tooling / license | Source or config IP held by | Support contract & SLA |
|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-CTL-01 | | | | `{{utility? project? both?}}` | | | |
| IT-CTL-02 | | | | | | | |

**Zone-specific checks — the highest-risk zone**

- [ ] **Who owns and who programs the utility RTAC/RTU?** These are frequently utility-owned and utility-programmed, meaning the project cannot touch the device its own telemetry depends on. Establish the escalation path and the realistic response time *before* it fails.
- [ ] **Is the control logic source code held, and by whom?** RTAC logic, PPC control programs, HMI projects, relay settings files. A replacement controller with no source is a brick. Check whether the contract grants a source-code or configuration deliverable, and whether it was ever actually delivered — the [Warranty Obligation Matrix](../../Warranty_Obligation_Matrix%28WOM%29/warranty-obligation-matrix.md) and the EPC/LTSA closeout deliverables are where this lives. **Escrow if it is not.**
- [ ] Is the programming **toolchain** available — the right software version, a valid license, a machine that runs it, and someone trained?
- [ ] Are **relay settings files** under version control, and does anyone hold the as-commissioned set?
- [ ] Does reprogramming require **utility witness or re-commissioning**, and how long does that take?
- [ ] Is the **meter** sealed such that replacement triggers a utility/ISO re-certification? What is that lead time?
- [ ] Are changes to any of these under **change control** — many are warranty- or compliance-relevant.

### 4.5 Compute & storage

Zone `SRV`: SCADA server(s), historian, HMI workstations, virtualization host, NAS/storage, backup system, edge collector.

**A — Redundancy**

| ID | Component | Make / model | Location | Redundant? | Failover auto/manual | Last failover test | Spare | Status |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-SRV-01 | SCADA server | | Rack | | | | | |
| IT-SRV-02 | Historian / collector | | | | | | | |
| IT-SRV-03 | HMI workstation | | | | | | | |
| IT-SRV-04 | Storage / NAS | | | `{{RAID level}}` | | | | |

**B — Recoverability**

| ID | Backup location | Last backup | **Restore tested** | Who can rebuild | Their tooling / license | Source or config IP held by | Support contract & SLA |
|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-SRV-01 | | | | | | | |

**Zone-specific checks**

- [ ] **Has a restore actually been performed?** A backup that has never been restored is a hypothesis. This is the single most valuable check in the study.
- [ ] Is a backup held **off-site**, and does anything protect it from an on-site event?
- [ ] Are **software licenses** tied to hardware IDs or dongles that would not transfer to replacement hardware?
- [ ] Is the SCADA/historian **application version** recorded, along with its installer and license keys?
- [ ] What is the **data retention** on site, and how much data is lost if the collector is down for a week? Cross-reference the availability data the [Performance Guarantee Matrix](../../Performance_Guarantee_Matrix%28PGM%29/performance-guarantee-matrix.md) depends on.
- [ ] Does the platform **buffer and backfill** during a comms outage, or is the gap permanent? A permanent gap is a dispute.

### 4.6 Power to the comms stack

Zone `PWR`: UPS, rack PDUs, station service, DC systems, generator, the breakers feeding all of the above.

**A — Redundancy**

| ID | Component | Make / model | Location | Redundant? | Failover auto/manual | Last failover test | Spare | Status |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| IT-PWR-01 | UPS — control building | | | | | | | |
| IT-PWR-02 | UPS batteries | | | | n/a | `{{last load test}}` | | |
| IT-PWR-03 | Rack PDU | | | `{{A/B feeds?}}` | | | | |
| IT-PWR-04 | Station service / aux supply | | | | | | | |
| IT-PWR-05 | DC system (`{{125 VDC}}`) | | Substation | | | | | |
| IT-PWR-06 | Backup generator | | | | | | | |

**Zone-specific checks — the most common real cause of "network failure"**

- [ ] **Is every device in §4.1–4.5 actually on the UPS?** Walk the rack physically. There is always one that is not, and it is always important.
- [ ] What is the **measured** UPS runtime at present load — not the nameplate, and not the runtime when the batteries were new?
- [ ] **How old are the UPS batteries**, and when were they last load-tested? This is a scheduled-replacement item that should already be in the spares plan.
- [ ] Does the UPS **alarm to somewhere a human sees**, and is that alarm path itself dependent on the equipment the UPS protects?
- [ ] On extended outage, does the stack **shut down gracefully** or drop mid-write?
- [ ] Are the **breakers feeding the rack** labelled and documented, so nobody de-energises site comms during unrelated work?
- [ ] Is the DC system's **battery bank** on a test schedule, and who owns it — BOP O&M or the substation LTSA?

### 4.7 Environment & time

Zone `ENV`: control-building HVAC, temperature/humidity monitoring, fire detection and suppression, physical security, GPS clock and time sync.

| ID | Component | Redundant? | Spare | Monitored / alarmed to | Who maintains | Status |
|:---|:---|:---|:---|:---|:---|:---|
| IT-ENV-01 | Control building HVAC | | | | | |
| IT-ENV-02 | Rack temperature monitoring | | | | | |
| IT-ENV-03 | Fire detection / suppression | | | | | |
| IT-ENV-04 | GPS clock & antenna | | | | | |
| IT-ENV-05 | NTP source | | | | | |

**Zone-specific checks**

- [ ] Does **HVAC failure alarm** before equipment thermal-shuts-down, and to whom?
- [ ] Is **time sync** traceable and monitored? Bad time corrupts settlement intervals and makes event logs across systems unmatchable — an underrated failure because nothing announces it. Confirm the GPS antenna, its cable, and its surge protection are inventoried like any other component.
- [ ] Does anything **depend on a single GPS receiver**, and what happens to the meters and relays if it drifts?

## 5. Cross-cutting checks

Not tied to one device — these are the systemic answers.

| Check | Finding |
|:---|:---|
| **Config backup regime** — is there one policy covering every device, automated, verified? Or per-device folklore? | |
| **Credential and license vault** — where, who can reach it, what happens if that person is unreachable? | |
| **Firmware currency** — anything end-of-support, or blocked from patching by a warranty/change-control condition? | |
| **Monitoring the monitors** — if the historian or the alarm path itself fails, who finds out and how? | |
| **Documentation currency** — do the network diagram, IP schedule, and rack elevation match reality? Date them. | |
| **Key-person dependency** — is any device recoverable by exactly one named individual? | |
| **Vendor reachability** — support contracts in force, out-of-hours numbers verified this year? | |
| **Change log** — is there a record of what changed on the network since the last study? | |

## 6. Findings

### 6.1 Single points of failure

Ranked by consequence, not by device cost.

| Rank | ID | Component | What is lost | Time to restore today | Commercial consequence | Recommendation |
|:---|:---|:---|:---|:---|:---|:---|
| 1 | | | | | `{{LD exposure, telemetry penalty, data gap}}` | |

### 6.2 Spares gaps

Feeds the [Spare Parts Review](../index.md).

| ID | Component | Lead time if ordered today | Outage tolerated by the guarantee | Gap | Recommended holding |
|:---|:---|:---|:---|:---|:---|
| | | | | | |

### 6.3 Recovery & knowledge gaps

| ID | Component | Gap type (config / source / license / people) | Detail | Owner | Action |
|:---|:---|:---|:---|:---|:---|
| | | | | | |

### 6.4 Untested redundancy

Everything marked **R?** in §4 — believed redundant, never proven. Feeds the failover test study.

| ID | Component | Redundancy claimed | How it would be tested | Test window required |
|:---|:---|:---|:---|:---|

## 7. Actions & handoffs

| # | Action | Type | Owner | Due | Handoff to |
|:--|:---|:---|:---|:---|:---|
| 1 | | Spare purchase | | | Spare Parts Review |
| 2 | | Procedure | | | `Standard_Operating_Procedures(SOP)/` |
| 3 | | Test | | | Failover / Redundancy Test Study |
| 4 | | Contract / commercial | | | `{{source-code escrow, support SLA, spares obligation}}` |
| 5 | | Documentation | | | |

Open items also go to this folder's `todo.md`.

## 8. Failure lookup

**The 2 AM page.** Find the symptom, get the component, and §4 gives redundancy, spare, and who to call.

| Symptom | Suspect first | Check | If confirmed |
|:---|:---|:---|:---|
| All site comms lost, plant otherwise healthy | IT-PWR-01/02 (UPS), then IT-WAN-01/02 | Rack powered? Carrier status? | `{{SOP}}` |
| ISO telemetry stale, everything else fine | IT-CTL-01 (RTAC), telemetry path | Is the path shared or diverse? | Utility escalation — note the ownership boundary |
| One BESS block unreachable | IT-NET-02/03 (field switch, fiber) | Ring intact? Switch powered? | Spare switch — needs config restore |
| SCADA/historian down, field healthy | IT-SRV-01/02 | Buffering? Will it backfill? | Data gap → flag for the availability record |
| Data present but timestamps wrong | IT-ENV-04/05 (GPS, NTP) | Time source locked? | Stop settlement submissions until resolved |
| Remote access lost, plant reachable locally | IT-SEC-01/02 | License expired? Cert expired? | `{{SOP}}` |

Populate the *If confirmed* column with real SOP references as they are written — this table is the main reason those SOPs exist.

## 9. Review

Annual, plus after any material change to the network, a controller replacement, or a comms-related outage.

| Review date | Performed by | Components changed since last | Findings closed | New findings |
|:---|:---|:---|:---|:---|

Detailed history lives in this folder's `log.md`, not here.
