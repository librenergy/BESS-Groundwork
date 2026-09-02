---
type: Template
title: Owner Metric Calculation Sheet Template
description: "The per-metric calculation sheet: copy per owner metric as MT_<SEG_TYPE>_<NNN>-<name>.md. Self-contained: formula, inputs with their sources of truth, boundary, clock, target/alert, and reconciliation checks."
---

# `MT_{{SEG_TYPE}}_{{NNN}}` — `{{Metric Name}}`

**Class:** engineering | monitoring · **Segment grain:** `{{SEG_TYPE}}` · **ONT version:** `{{ONT_VERSION}}` · **Status:** defined | implemented

**What it measures — and what it does not.** One paragraph: the plain-language definition, and the near-miss quantities it explicitly is *not* (capability vs delivered energy vs setpoint-following are the classic confusions). If it shadows or evidences a performance guarantee, say which PG-xxx and how the two must never share an identifier.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_{{SEG_TYPE}}_{{NNN}}` |
| **Formula** | written in ratified [measurement terms](../../Ontology%28ONT%29/measurements.md); state null/gap handling explicitly (a gap is never silently zero *or* silently ignored — pick and say why) |
| **Units** | |
| **Measurement boundary** | POI / POM / equipment AC terminals / DC bus, with directional loss treatment |
| **Inputs & sources of truth** | one line per input: the canonical column or feed it reads, **which system wins** when several report it, and why (meter for money; the controlled test over telemetry; never verify a counterparty's number with the counterparty's data) |
| **Secondary / cross-check source** | |
| **Calculation interval** | and the minimum valid intervals per aggregation period |
| **Aggregation** | how raw → interval → daily → period |
| **Timezone & clock convention** | engineering series (calendar) vs a contract clock — never annualize across the wrong one |
| **Exclusions** | typically none for an owner metric (raw physical series); if any, per the governing document |
| **Target / alert** | the threshold this metric is held against, if it carries one, and its review cadence; owner metrics have no contractual targets — those live with their PG in the PGM |
| **Related PG** | `shadows` / `evidences` / `feeds` / `leads` + PG-xxx, or — |
| **Owner of the calculation** | who computes the official number, and where (materialized in the platform vs computed at read) |
| **Reporting chain** | which products read this series (daily report, dashboards, monthly report) |
| **Reconciliation / plausibility checks** | the checks this metric carries: bounds vs ratings, step-change investigation, meter-vs-controller tolerance, flag handling (contractual flags logged as events, never dropped) |
| **Known discrepancies** | expected deltas vs related contractual metrics, stated so nobody "fixes" them |
