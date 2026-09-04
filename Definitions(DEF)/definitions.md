---
type: Template
title: Definitions — Global Glossary
description: "Every acronym, term of art, and organization used across the repo, defined once with its project-specific meaning; ambiguous terms carry explicit warnings."
---

# Definitions — Global Glossary

**Version:** v0.0 (template) · **Populated:** {{date}} from a full-repo scan · **Maintained by:** the `definitions-taxonomy` skill

One glossary for the whole repo. Definitions give the **project meaning**, not the textbook one: where two contracts define the same term differently, both definitions appear, because the difference is the point. Terms with two meanings in the repo are flagged ⚠️ in [§6](#6-️-ambiguous--tricky-terms). Which name to *use* when synonyms exist is decided in [taxonomy.md](./taxonomy.md).

> **How to populate (see the skill for the full workflow):** scan every markdown file plus source-document filenames; include terms even when they seem obvious; give each entry the meaning *as used on this project* with a source reference; never guess a legal entity name: mark it ❓ and log an open item. The starter rows below are generic seeds; replace or extend with the project's actual terms.

## 1. Organizations & parties

One row per organization, with **every alias/spelling seen in source documents** (contracts routinely spell the same party three ways).

| Term | Role on this project | Aliases / spellings seen | Source |
|---|---|---|---|
| ❓ {{Owner SPV legal name}} | Asset Owner / project company; note its defined role-name under each contract (e.g. "Seller", "Service Provider", "Buyer") | | |
| ❓ {{Offtaker}} | Offtaker / revenue counterparty | | |
| ❓ {{OEM / integrator}} | Battery/PCS/EMS OEM, LTSA provider | | |
| ❓ {{EPC}} | EPC contractor(s) | | |
| ❓ {{O&M provider}} | O&M / GOP / ROCC | | |
| ❓ {{ISO/BA, TO, SC}} | Market and grid roles | | |
| ❓ {{AM / Insurer / Lender / IE}} | Finance ring | | |

## 2. Contracts & commercial

| Term | Expansion | Meaning on this project | Source |
|---|---|---|---|
| **LTSA** | Long Term Services Agreement | | |
| **PPA / Tolling Agreement** | Power Purchase / Energy Storage Tolling Agreement | State which instrument actually governs; folder names can mislead | |
| **EPC(A)** | Engineering, Procurement & Construction (Agreement) | | |
| **LGIA / GIA** | (Large) Generator Interconnection Agreement | | |
| **LD(s)** | Liquidated Damages | | |
| **COD** | Commercial Operation Date | | |
| **NTP** | Notice to Proceed | | |
| **FM** | Force Majeure | ⚠️ Compare definitions across contracts; they are rarely identical | |
| **SLA** | Service Level Agreement | | |
| **SPV** | Special Purpose Vehicle | | |
| **TBC / TBD** | To Be Confirmed / To Be Determined | Repo-wide markers for unverified facts | |

## 3. Technical & equipment

| Term | Expansion | Meaning on this project | Source |
|---|---|---|---|
| **BESS** | Battery Energy Storage System | | |
| **AC Block (ACB)** | — | Integrated PCS+battery container with AC output; the leaf unit at owner level, no separate Bus or Bat visible | [`taxonomy.md`](taxonomy.md) §2b |
| **DC Block** | — | DC battery container with no inverter; connects to an external PCS. Carries the `Bat` segment code in topic paths | [`taxonomy.md`](taxonomy.md) §2b |
| **ES** | Energy Station | The combination level: the blocks behind one block transformer: multiple AC blocks + TX (AC station), or multiple DC blocks + one or more PCS + TX (DC station) | [`taxonomy.md`](taxonomy.md) §2b |
| **BMS** | Battery Management System | Note the hierarchy actually deployed (string/master) | |
| **PCS** | Power Conversion System | Note whether separate skids or integrated in the enclosure | |
| **EMS** | Energy Management System | Name the actual device/vendor product | |
| **PPC** | Power Plant Controller | | |
| **SCADA** | Supervisory Control and Data Acquisition | | |
| **RTAC** | Real-Time Automation Controller | | |
| **POI** | Point of Interconnection | ⚠️ Check for a differently-named LTSA measurement point (POM etc.) | |
| **BOP** | Balance of Plant | Usually the owner's uncovered risk class; say what it includes here | |
| **APM** | Asset Performance Management | The owner-side analytics platform | |
| **CMMS** | Computerized Maintenance Management System | | |
| **OMS** | Outage Management System | | |
| **ROCC / GOP** | Remote Operation Control Center / Generator Operator | | |
| **CT / PT** | Current / Potential Transformer | | |
| **Historian** | — | The owner-side time-series data platform storing telemetry for shadow calculations | |

## 4. Market & grid

| Term | Expansion | Meaning on this project | Source |
|---|---|---|---|
| **ISO / RTO / BA(A)** | Independent System Operator / Regional Transmission Organization / Balancing Authority (Area) | ⚠️ If the project sits in a non-ISO BA, every ISO-centric template row must be remapped | |
| **SC / QSE** | Scheduling Coordinator / Qualified Scheduling Entity | | |
| **TO / TOP** | Transmission Owner / Operator | | |
| **LMP** | Locational Marginal Price | | |
| **DA / RT** | Day-Ahead / Real-Time | | |
| **AS / AGC** | Ancillary Services / Automatic Generation Control | | |
| **RA** | Resource Adequacy | Name the program administering it in this market | |
| **NERC** | North American Electric Reliability Corporation | | |
| **GADS** | Generating Availability Data System (NERC) | The industry outage/derate event taxonomy (Conventional/Wind/Solar; standalone BESS GADS pending); the toolkit's `Data_Product(DP)/Outage_Tracker` document runs a GADS-aligned BESS taxonomy | `Data_Product(DP)/Outage_Tracker/` |

## 5. Metrics

| Term | Expansion | Meaning on this project | Source |
|---|---|---|---|
| **OBE** | Overall Battery Effectiveness | The BESS analogue of manufacturing's OEE: the average across intervals of `min(EA, OA, PA, QA)`. The weakest of the four availability types sets the ceiling in every interval | [Daily Performance Report §2](/Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) |
| **EA** | Equipment Availability | Is the BESS on? Built bottom-up from contactor and status signals: `min` at string, `min` of battery side and conversion side at the DC bus. Blind to imbalance, SOC error, and derates | Daily Performance Report §2.1 |
| **OA** | Operational Availability | Is it responding to dispatch? Meter vs setpoint, with a ramp-widened dead-band and a **directional** SOC exclusion. The most accurate of the four, but only exists while the plant is dispatched | Daily Performance Report §2.2 |
| **PA** | Power Availability | Can it deliver full power now? ⚠️ Two competing definitions: the correct one is `min(ACP, ADP)` away from the SOC rails; some service agreements use `max(...)`, which cannot see a one-sided derate | Daily Performance Report §2.3 |
| **QA** | Energy Capacity | Does it still hold the contracted MWh? `min(Q_test / Q_guaranteed, 100%)` from the most recent controlled capacity test. ⚠️ A **scalar**, not an interval series: layering it per interval double-counts what EA, OA and PA already capture. Renamed from "Energy Availability" 2026-09-02: it is a capacity-retention number, not an availability of energy (that would be state of charge). The `QA` acronym is kept as the stable ID | Daily Performance Report §2.4 |
| **ACP / ADP** | Available Charge Power / Available Discharge Power | The two directional power-capability signals, from the BMS at DC-bus level and the PCS per module. Keep them separate until the final combination step | Daily Performance Report §2.3 |
| **RTE** | Roundtrip Efficiency | ⚠️ Expect per-contract variants (boundary, temperature adjustment, guaranteed values) | |
| **SOC / SOE** | State of Charge / State of Energy | ⚠️ Watch units (% vs MWh) **and basis** (installed vs contracted) across sources. A plant at 100% contracted SOC may be at 95% installed SOC with batteries that are not full | |
| **Overbuild** | — | Installed capacity above contracted capacity. Absorbs unit outages before the contract position is touched; the gap closing is the earliest warning that guarantee margin is being consumed | |
| **SOH** | State of Health | | |
| **Availability** | — | ⚠️ Expect one definition per contract; record each formula and its exclusions | |
| **EFC** | Equivalent Full Cycles | Compare usage rights vs guarantee termination thresholds | |
| **MTTR** | Mean Time To Respond/Repair | | |
| **Equivalent Outage Hour** | — | Derate duration × (unavailable MW ÷ capacity base) | |
| **Outage codes** | e.g. PO / FO-x / EXT / FM / OD / DR | The outage taxonomy driving availability accounting | |

## 6. ⚠️ Ambiguous & tricky terms

The terms that cause real disputes: two meanings in the repo, or two contractual definitions of the same word. Populate aggressively; this section earns the document its keep. Classic patterns to check for:

| Term | Meaning A | Meaning B | Why it bites |
|---|---|---|---|
| **SC** | Scheduling Coordinator (market role) | Substantial Completion (milestone) | Same two letters in different layers; never abbreviate the milestone. |
| **EIM** | Entity Interaction Map (this toolkit) | Energy Imbalance Market (WEIM) | If the project touches the WEIM, always write **WEIM** for the market. |
| **Availability** | Offtake-contract definition | LTSA definition | Same headline %, different formulas, exclusions, and data sources; usually neither derivable from the other. |
| **Capacity** | Contractual (contract capacity, tested capacity, ratio) | Physical (installed, rated, usable) | Count the distinct "capacities" in play and name each. |
| **{{owner role names}}** | "Seller"/"Service Provider" in one contract | "Buyer"/"Customer" in another | The same entity carries different defined names per contract. |
| **Year** | Contract Year (offtake clock) | Operating Year (LTSA clock) | Different start dates ⇒ guarantee windows never align. |

## 7. Repo & methodology

| Term | Expansion | Meaning | Source |
|---|---|---|---|
| **EIM** | Entity Interaction Map | The anchor topology from which all satellite documents derive | `Entity_Interaction_Map(EIM)/` |
| **Satellite documents** | — | The documents deriving from the EIM, built in the README's sequence | `README.md` |
| **EIM_VERSION** | Header field | Version stamp tracing a populated document to a specific EIM version | templates |
| **OKF** | Open Knowledge Format v0.2 | The repo's documentation convention (frontmatter, reserved index/log, per-folder indexes) | `AGENTS.md` |
| **RACI** | Responsible / Accountable / Consulted / Informed | Role-assignment matrix; exactly one **A** per row | `RACI_Matrix(RACI)/` |
| **KPI** | Key Performance Indicator | One authoritative definition per metric | `Metrics_Tree(MT)/` (the Metrics Tree) |
| **Shadow calculation / shadow settlement** | — | Owner-side independent replication of a counterparty's contractual number | `Performance_Guarantee_Matrix(PGM)/` |
| **`%% TODO` / ❓** | Mermaid comment / open-question marker | Conventions for recording uncertainty without pretending a fact is known | EIM, skills |

## Open items

- [ ] {{Terms used but never expanded in any source; confirm against source documents}}
- [ ] {{Party-name/entity ambiguities, carried as flagged aliases, never silently unified}}
