---
type: Template
title: Settlement Reconciliation Procedure (Shadow Settlement)
description: "Independently verifies ISO settlement statements against the project's own metering, awards, and dispatch records."
---

# Settlement Reconciliation Procedure (Shadow Settlement)

**Project:** `{{PROJECT_NAME}}`  **Market/ISO:** `{{ISO}}`  **Version:** `{{VERSION}}`  **Last updated:** `{{DATE}}`
**Source EIM version:** `{{EIM_VERSION}}`  **Companion docs:** Data Interface Register (meter & market interfaces), Contact Register (SC, ISO)

> Purpose: independently verify ISO settlement statements against the project's own metering, awards, and dispatch records — catching meter bugs, missing intervals, mispriced awards, and misapplied charges before dispute windows close. Per the EIM, the chain is: POI revenue meters → ISO settlement system → AO, cross-checked against Trade & Award Data via Optimizer/SC.

## 1. Data Inputs

| Input | Source (EIM node) | Access | Granularity | Latency | Owner |
|-------|--------------------|--------|-------------|---------|-------|
| ISO settlement statements (initial / re-settlement runs) | `SET` via SC | | per ISO | per ISO calendar | SC |
| Revenue meter interval data | `POI` | | {{e.g., 15-min, interval-beginning, CPT/PPT}} | | |
| Trade & award data | `TAD` via API | | | | OPT/SC |
| Dispatch instructions / basepoints | SC / EMS records | | | | |
| Telemetry (plant actuals) | data platform | | | | PE |
| Market prices (LMP, AS clearing) | `MD` | | | | |

## 2. Meter Data Validation (run first — garbage in, disputes out)

| Check | Rule | Action on Failure |
|-------|------|-------------------|
| Completeness | all intervals present per trade date | gap-fill per ISO rules; flag to SC |
| Duplicates | dedupe by `{{e.g., SAVECHANNEL / interval key}}` | keep latest version |
| Sentinel / rollover values | detect UINT32 max, frozen values, register rollovers | replace per validated estimate; log meter ticket |
| Sign convention | charge vs. discharge orientation consistent | |
| Timezone / DST | intervals align to ISO convention ({{TZ}}) | |
| Cross-check vs. PPC | meter kWh vs. integrated PPC power within {{TOL}}% | investigate CT/PT, scaling |

## 3. Shadow Calculation

Per settlement interval, compute expected charges/credits per stream and compare to the statement:

| Stream | Shadow Formula (summary) | Inputs | Tolerance |
|--------|--------------------------|--------|-----------|
| Energy (DA) | DA award × DA LMP | TAD, MD | {{$/interval}} |
| Energy (RT deviations) | (metered − DA schedule) × RT LMP | POI, TAD, MD | |
| Ancillary services capacity | awarded MW × clearing price | TAD, MD | |
| AS performance adjustments (mileage, accuracy) | per ISO method | telemetry, MD | |
| Non-performance penalties | per ISO method | dispatch vs. actuals | |
| Charges (transmission, uplift, admin) | per statement category | | reasonableness |

> Implementation note: shadow logic lives in `{{REPO/dbt models/notebook}}`; statement parsing in `{{PIPELINE}}`.

## 4. Monthly Workflow & Calendar

| Step | When | Owner | Output |
|------|------|-------|--------|
| 1. Ingest statements + meter + TAD | statement publication + {{N}}d | PE pipeline | loaded tables |
| 2. Meter validation (§2) | same day | PE | validation report |
| 3. Shadow vs. statement diff | +1d | PE | exception list |
| 4. Review exceptions with SC | +3d | AM/PE/SC | dispute candidates |
| 5. File disputes | before ISO deadline: {{DEADLINE per run}} | SC | dispute refs |
| 6. Track re-settlement runs | per ISO calendar (e.g., initial → re-runs at {{T+...}}) | SC | closure |
| 7. Post reconciled revenue to accounting | month-end | AM | ledger entry (`AM_A` per EIM) |

## 5. Exception & Dispute Log

| # | Trade Date(s) | Stream | Statement $ | Shadow $ | Delta | Root Cause | Dispute Filed (ref/date) | Status | Recovered $ |
|---|---------------|--------|-------------|----------|-------|-----------|---------------------------|--------|-------------|
| | | | | | | | | | |

## 6. Known Issue Patterns (project-specific watchlist)

| Pattern | Detection | History |
|---------|-----------|---------|
| Meter sentinel values (e.g., UINT32 max on SEL-class meters) | §2 check | |
| Stale telemetry causing AS performance penalties | telemetry uptime KPI | |
| Award/schedule mismatch after exceptional dispatch | TAD vs. dispatch diff | |
| | | |
