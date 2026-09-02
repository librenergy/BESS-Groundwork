---
type: Template
title: Platform Projection
description: "The agent build spec: how a data-platform agent turns the ratified ontology layers into dimension data, canonical tables, and column comments on a real platform. One reference implementation, explicitly swappable."
---

# Platform Projection

**Agent build spec** · part of the [Groundwork Ontology](ontology.md)

This file is the handoff point between the ontology (Markdown, platform-agnostic) and a real data platform. It is written to be executed by an AI agent with database access: the directives below say exactly what to build from the three ratified layers. The reference implementation assumes the common split of **dimension store** (an OLTP database such as Postgres, where dimension data is authored) replicated into an **analytics warehouse** (such as ClickHouse, where fact data lives); adapt the DDL dialect to your stack and record deviations in this file's project copy.

## Design decisions to ratify once (project fills in)

| Decision | Value | Notes |
|:---|:---|:---|
| Dimension store / warehouse | `{{PLATFORMS}}` | e.g. Postgres replicated to ClickHouse |
| Replication mechanism | `{{REPLICATION}}` | CDC tool, FDW, dictionary, or batch export |
| Timestamp convention | `{{INTERVAL_CONVENTION}}` | interval-beginning vs interval-ending, stated once for all canonical tables |
| Historian layer | `{{HISTORIAN}}` | the narrow (one row per reading) system-of-record table(s) the canonical tables are built from |
| Canonical build mechanism | `{{BUILD_MECHANISM}}` | e.g. dbt incremental model, refreshable materialized view, scheduled INSERT...SELECT |
| Build cadence + lookback | `{{CADENCE_LOOKBACK}}` | how often the pivot runs and how far back each run re-upserts for late data |
| State staleness cap | `{{STALENESS_CAP}}` | how long a state term (e.g. `connected`) carries forward before going null |
| Canonical table naming | `{{NAMING}}` | default: `canonical.<seg_type_lower>_generic` |
| Currency unit | `{{CURRENCY}}` | fixes the `*` units in the measurement vocabulary |
| `ONT_VERSION` this build reflects | `{{ONT_VERSION}}` | re-run the build when it changes |

## The relational shape (reference)

Four ontology tables, authored in the dimension store beside the existing dimensions, replicated to the warehouse read-only:

```sql
-- 1. Segment taxonomy  (from segment-types.md)
CREATE TABLE ont_seg_type (
  seg_type_id   text PRIMARY KEY,      -- e.g. 'Bat_Str'
  name          text NOT NULL,
  parent_type   text REFERENCES ont_seg_type(seg_type_id),
  definition    text NOT NULL,
  synonyms      text[] DEFAULT '{}'
);

-- 2. Measurement vocabulary  (from measurements.md)
CREATE TABLE ont_measurement (
  term          text PRIMARY KEY,      -- e.g. 'cell_voltage'
  kind          text NOT NULL CHECK (kind IN ('measurement','state','parameter')),
  unit          text NOT NULL,         -- exactly one; '' only for enums
  data_type     text NOT NULL,
  definition    text NOT NULL,
  is_not        text,                  -- the near-miss disambiguation
  synonyms      text[] DEFAULT '{}'
);

-- 3. Canonical models  (from canonical-models.md; one row per column)
CREATE TABLE ont_canonical_model (
  seg_type_id   text NOT NULL REFERENCES ont_seg_type(seg_type_id),
  column_name   text NOT NULL,         -- e.g. 'cell_voltage_max'
  term          text NOT NULL REFERENCES ont_measurement(term),
  statistic     text,                  -- avg | min | max | sum | NULL
  required      boolean NOT NULL,
  notes         text,
  PRIMARY KEY (seg_type_id, column_name)
);

-- 4. Metric registry  (IDs, class, and pointer only; definitions live in exactly one home)
CREATE TABLE ont_metric (
  metric_code      text PRIMARY KEY,      -- contract suffix for PG metrics, e.g. 'storage_availability_tolling'
  name          text NOT NULL,
  class         text NOT NULL CHECK (class IN ('performance_guarantee','engineering','monitoring')),
  inputs        text[] DEFAULT '{}',   -- ont_measurement terms and metric_codes (may name a data product, e.g. the outage log)
  doc_ref       text NOT NULL          -- the ONE definition home: PGM calc sheet for performance_guarantee, Metrics Tree sheet/row otherwise
);
```

The project's segment dimension (instances) then references `ont_seg_type`, and its signal/tag dimension references `ont_measurement`: the ontology becomes a foreign-key-enforced vocabulary rather than a convention.

## Agent directives

An agent asked to "build the dimension data" or "build the canonical data model" executes, in order:

1. **Read the three layer files** (`segment-types.md`, `measurements.md`, `canonical-models.md`) from the project clone. Treat rows marked `❓` as not ratified: skip them and report them, never guess them.
2. **Upsert the ontology tables** from the tables in those files, in dependency order (seg types → measurements → canonical model rows → metric registry from the Metrics Tree's `metric_code` column). Deletions require human confirmation; ontology rows are load-bearing identifiers.
3. **Generate one canonical table per ratified model**: the standard envelope columns plus one column per `ont_canonical_model` row, typed from the vocabulary, nullable where `required = false`. Apply the project's engine conventions (partitioning, ordering, TTL) from the existing canonical tables; the ontology fixes names, types, units, and comments, not storage tuning.
4. **Write column comments everywhere.** Every canonical-table column gets a comment of the form `[ONT:<term>] <definition> [<unit>]`, and every dimension column that stores a controlled value gets `[ONT:...]` likewise. This is what makes the ontology answerable from inside the database: "what is state of charge" must be answerable from the system catalog alone.
5. **Validate**, and report failures rather than fixing silently:
   - every canonical-table column beyond the envelope has an `ont_canonical_model` row, and vice versa;
   - every model row's `term` exists in `ont_measurement`, and column naming follows `term[_statistic]`;
   - no term appears with two units anywhere in the platform's signal dimension;
   - every segment instance's type exists in `ont_seg_type`;
   - every `metric_code` in dashboards or reports exists in `ont_metric`.
6. **Stamp the build**: record the `ONT_VERSION` built against (in this file's decision table and the folder `log.md`), so drift between repo and platform is detectable.

## What this projection is not

- It is **not the binding layer**: which vendor tag feeds `cell_voltage_max` at site X lives in the Data Interface Register and the platform's mapping tables.
- It is **not a semantic layer**: it does not execute metrics or sit in the query path. Grafana panels and notebooks keep their SQL; the ontology makes their vocabulary findable and unambiguous, which is a lookup problem, not an execution problem.
- It is **not mandatory shape**: a project on a historian or a single-database stack keeps the four logical tables and the comment discipline and drops the replication split.
