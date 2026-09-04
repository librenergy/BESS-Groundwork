---
type: Template
title: "MT_BESS_008 — Telemetry health"
description: "Owner metric: valid intervals against expected, gaps and stale tags for the signals every other sheet reads, checked against the contractual validity floor. Feeds PG_201."
---

# `MT_BESS_008` — Telemetry health

**Class:** monitoring · **Segment grain:** `BESS` · **ONT version:** `{{ONT_VERSION}}` · **Status:** template

> Generic sheet from the master repo. `{{PLACEHOLDERS}}` and ❓ are project-fill fields; the formula and the rules around it are the toolkit's method and change only with a logged reason. Registry row: [Metrics Tree](../metrics-tree.md#core-metrics-by-segment-starter-registry).

**What it measures, and what it does not.** Whether the data behind every other number is **there and believable**: the share of expected samples that arrived and passed validation, the gaps, and the tags that stopped changing. Reported for the critical tag groups (the inputs of `MT_BESS_002` to `_005` and the settlement meter) and rolled up as the weakest group. It is **not** equipment availability (a plant can be fully in service and unmeasured) and not the contractual validity rule itself. `PG_201`'s minimum valid intervals per hour is the floor this metric is held against, and the reason a bad data day becomes a money day.

| Field | Value |
|-------|-------|
| **metric_code** | `MT_BESS_008` |
| **Formula** | per tag and period: `validity = valid_samples / expected_samples`, where valid means present, in range, and not stale; `stale` = unchanged for longer than the tag's expected variability window `{{w}}` while the source `heartbeat` advances (a flat value with a dead heartbeat is a gap, not stale); `gap` = a run of missing samples longer than `{{g}}` intervals, counted and summed in minutes. Per tag group: `validity_group = min over its tags`. Site headline: `min over critical groups`. Hourly: the count of valid intervals against the contractual floor. **Gaps** are the measurement; nothing is imputed. |
| **Units** | % validity; count and minutes of gaps; count of stale tags |
| **Measurement boundary** | the platform's ingestion tables, per source system as mapped in the [Data Interface Register](../../Data_Interface_Register%28DIR%29/data-interface-register.md) |
| **Inputs & sources of truth** | raw ingested samples per tag (**the platform's own landing tables**, before any cleaning); `heartbeat` per source; expected sample rate per tag from the DIR; the critical tag groups defined here: `{{EA status tags}}`, `{{OA setpoint + meter}}`, `{{PA capability tags}}`, `{{SOC}}`, `{{settlement meter}}` |
| **Secondary / cross-check source** | source-side counts where a system exposes them (historian record counts, meter interval counts) to separate a source outage from an ingestion outage |
| **Calculation interval** | hourly; daily and monthly roll-ups |
| **Aggregation** | tag → group (`min`) → site (`min`) per hour; period values are the share of hours meeting the floor, not the mean validity |
| **Timezone & clock convention** | `{{TZ}}`; timestamp drift between sources is itself a finding (alert when `> {{s}}` s) |
| **Exclusions** | none. A planned platform outage is logged as an event; it still counts here |
| **Target / alert** | held against `PG_201`'s validity floor (`{{n}}` valid intervals per hour, ❓ clause) and the owner's own floor `{{x}}`%. Alert on any critical group below the floor for an hour, and on any stale critical tag. Review daily; it is the first tile on the dashboard because everything else depends on it |
| **Related PG** | feeds `PG_201`; feeds every sheet in this folder |
| **Owner of the calculation** | `[PE]`, materialised hourly on the platform; the platform must be able to compute it about itself |
| **Reporting chain** | Daily Performance Report §4.1 (valid-interval count per hour, the hours at risk of dropping out of the denominator), dashboards, Monthly Performance Report |
| **Reconciliation / plausibility checks** | platform counts vs source counts; validity never above 100%; a gap on every tag of one source at once is that source's outage (one event), not many; clock-drift check across sources |
| **Known discrepancies** | The vendor's report may count validity on its own capture, which is not this. Where the owner's capture is worse than the vendor's, the vendor's number is uncontestable for those hours: that is the finding this metric exists to surface before year one closes |

## Easy to get wrong

Counting a flat value with a live heartbeat as valid. Averaging validity across the day, which hides the hours that fell below the contractual floor. Measuring after cleaning, which measures the cleaner. Not defining the critical groups, so the headline is the average of everything including tags nobody reads.
