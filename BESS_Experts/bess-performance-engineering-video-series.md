---
type: Reference
title: BESS Performance Engineering Video Series
description: "The video series behind this toolkit's availability methodology: episode guide, slide decks, and a map from each episode to where its content is implemented in the repo."
---

# BESS Performance Engineering Video Series

▶ **[Watch the playlist on YouTube](https://www.youtube.com/playlist?list=PL6YDF9vbRUPPZ-Cx-4w0VJZLA9s6kPtcN)**

The series this toolkit's availability methodology is built on. Module 1 defines the four BESS availability types and their arithmetic; Module 2 turns availability into the wider performance-engineering discipline the rest of the repo implements.

The slide decks are committed beside this file so an agent can read the source without leaving the repo. The videos carry the explanation; the decks carry the formulas.

> **Reference, never contractual.** Where a deck and an executed agreement disagree, the agreement governs. Where a deck and a [calculation sheet](../Performance_Guarantee_Matrix%28PGM%29/calculations/index.md) disagree, the calc sheet governs. Use these to understand *why* a calculation is shaped the way it is, not to settle what a project owes.

## Module 1: BESS Availability Explained

| # | Episode | Deck | What it establishes |
|---|---|---|---|
| E01 | Introduction to BESS Availability Types | [PDF](m01-e01-introduction-to-bess-availability-types.pdf) | Why one availability number is never enough. OEE in manufacturing becomes **OBE**, Overall Battery Effectiveness, built from four types with different granularity, coverage, accuracy and blind spots. Installed vs contracted capacity, overbuild, and the SOC basis problem (percent of installed is not percent of contracted) |
| E02 | Equipment Availability Deep Dive | [PDF](m01-e02-equipment-availability-deep-dive.pdf) | Measuring from contactor and BMS status signals. `min` at the string, `min` of battery side and conversion side at the DC bus, summed to site with an excused term. Voltage comparisons as signal quality control. POI normalisation of string and PCS capacities |
| E03 | Operational Availability Deep Dive | [PDF](m01-e03-operational-availability-deep-dive.pdf) | Shortfall as under-delivery only, the dead-band widened by ramp rate, and the **directional** SOC exclusion. Root causes of shortfall: SOC averaging hiding distribution, and the end-of-discharge taper driven by imbalance |
| E04 | Power Availability Deep Dive | [PDF](m01-e04-power-availability-deep-dive.pdf) | ACP and ADP as separate signals from the BMS and PCS function blocks. The weakest-string bus roll-up. The rail-aware `min(ACP, ADP)` combination. The six causes of available-power derate, and the corner cases to test for |
| E05 | Energy Availability Deep Dive | [PDF](m01-e05-energy-availability-deep-dive.pdf) | Why energy capacity is a **slow-moving signal** and layering it per interval double-counts the other three. `QA = min(Q_test / Q_guaranteed, 100%)`, entering OBE as a **scalar** that updates only on a capacity test or a guarantee change. Reading the test trace: the discharge taper is imbalance, not degradation |
| E06 | How to Process your Availability Data | [PDF](m01-e06-processing-availability-data.pdf) | `OBE = (1/N) Σ min(EA, OA, PA, QA)`. The path from EMS to inbox: warehouse, agent-assisted exploration, dashboards, and notebook-generated reports |

## Module 2: Performance Engineering as a Discipline

Availability tells you what happened; performance engineering is the practice that catches it, explains it, and closes it out. Module 2 covers what the discipline is, its three pillars (data infrastructure, daily engineering discipline, investigation management), the process from telemetry to action, and what it takes to run on a real fleet. Decks pending.

## Where each part lands in this repo

| Topic | Implemented in |
|---|---|
| The OBE framework and all four availability types | [Daily Performance Report §2](/Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) |
| Power availability as the calculation to negotiate into a service agreement | [Daily Performance Report §4.1](/Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) |
| Capacity normalisation to the POI | [Daily Performance Report §9.2](/Data_Product%28DP%29/Daily_Performance_Report/daily-performance-report.md) |
| Per-guarantee calculation methodology | [Calculation sheets](../Performance_Guarantee_Matrix%28PGM%29/calculations/index.md) |
| Terminology (OBE, EA, OA, PA, QA, ACP/ADP, overbuild) | [Definitions](/Definitions%28DEF%29/definitions.md) §5 |
| Entity interaction mapping (Module 2, pillar 1) | [Entity Interaction Map](/Entity_Interaction_Map%28EIM%29/entity-interaction-map.md) |
| Metrics tree (Module 2, pillar 1) | [Metrics Tree](/Metrics_Tree%28MT%29/metrics-tree.md) |
| Dashboards and the daily engineering discipline | [Dashboards](/Data_Product%28DP%29/Dashboards/dashboards.md), [Performance Engineering Management](/Data_Product%28DP%29/Performance_Engineering_Management/performance-engineering-management.md) |
