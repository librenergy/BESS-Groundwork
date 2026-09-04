# Calculation Sheets

One standalone sheet per guarantee mechanism, each carrying the complete calculation conditions so it can be reviewed independently. Built by the [`performance-guarantee-matrix`](/.agents/skills/performance-guarantee-matrix/SKILL.md) skill; name them `PG_{ID}-{contract}-{metric}.md` (the guarantee’s PG code first, so sheets sort by series).

* [Calc sheet template](calc-sheet-template.md) - The section structure every sheet follows, with a worked availability example.

A populated project typically ends up with one sheet per guarantee on each side of the stack: offtake availability, capacity, efficiency and dispatch compliance; supplier-side availability, capacity/retention and efficiency.

## Pseudo-SQL: the shared placeholder schema

Each sheet carries a **Pseudo-SQL** section showing how its calculation would look as a query. They all use the same placeholder schema, defined once here so the set reads as one data model rather than one naming convention per sheet.

**Illustrative only.** Names are placeholders; the real ones come from the historian design and the Data Interface Register. **Where a query disagrees with the formula on its sheet, the formula wins.** Contractual constants that are not yet known appear as `contract_param` lookups marked ❓, so the gap stays visible instead of being buried in a literal.

```sql
-- Contractual constants and not-yet-known values, one row per key.
contract_param(param_key, param_value_num, param_value_text, source_ref)
  -- 'contract_capacity_mw', 'guaranteed_power_capacity_mw', 'guaranteed_energy_capacity_mwh',
  -- 'contract_price_usd_kw_month', 'metering_system_error_pct', 'planned_outage_cap_h'

-- Year-by-year guarantee tables, one row per contract/metric/year.
guarantee_schedule(contract, metric, year_n, guaranteed_pct, throughput_limit_efc)
  -- contract: 'OFFTAKE' | 'SUPPLIER'   metric: 'AVAILABILITY' | 'EFFICIENCY' | 'RETENTION'

-- The authoritative input for event-based availability. One row per derate/outage.
outage_event(event_id, start_ts, end_ts, unavailable_mw,
             classification,        -- 'FORCED' | 'PLANNED' | 'FM' | 'TEST'
             notice_ts, notice_business_days, deferrable_flag, cause_code)

-- Performance tests: one row per test, with the T1-T8 meter reads and conditions.
perf_test(test_id, contract, test_ts, contract_year, operating_year,
          meter_import_t1_mwh, meter_import_t4_mwh,
          meter_export_t5_mwh, meter_export_t6_mwh,
          meter_export_t7_mwh, meter_export_t8_mwh,
          power_capacity_mw, ambient_max_c, ramp_rates_configured, test_result)

-- Revenue-meter interval data at the contractual measurement point.
meter_interval(ts, interval_min, import_mwh, export_mwh, quality_flag)

-- Offtaker dispatch instructions, one row per noticed interval.
dispatch_notice(hour_ts, notice_type, noticed_mwh, notice_ts, is_scarcity_event_hour)

-- OEM controller telemetry, at the guarantee's native resolution.
oem_telemetry(ts, dispatchable_charge_mw, dispatchable_discharge_mw, valid_flag)

-- Excuse/exclusion ledger - which contract excuses what, and for how much.
excuse_event(start_ts, end_ts, excused_mw, category, contract, evidence_ref)

-- Price series for any price-indexed adjustment.
price_series(trade_date, price_usd_mwh, index_name)
```

## What writing the queries tends to surface

Worth checking on every project; the exercise reliably exposes things the prose hides:

* **Which calculations read no telemetry at all.** Event-based availability and test-based capacity usually run entirely off the outage log and test records. No historian data substitutes for event-log discipline.
* **The excuse ledger needs a `contract` discriminator**, because the same physical event is routinely excused under one agreement and charged under the other.
* **Which inputs block on a document not held**: they show up as `contract_param` lookups with no value behind them.
* **Which conditions cannot be expressed in SQL at all.** Deemed-value rules (no row exists to query) and notice-deadline tests (whether an excuse row gets created is an upstream judgement) are the common two. Knowing this before scoping a build is the point.
