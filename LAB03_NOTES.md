# LAB03 Notes — Build Incremental Silver

## What I did

I prepared typed staging tables from the existing Bronze data and built the incremental Silver trip table. The flow normalized timestamps and labels, validated the driver relationship, deduplicated trip deliveries and then ingested the fixed late-trip batch. I reran the same logical inputs to prove idempotency and also executed the dbt-native validation graph.

## What I observed

The staging checks all passed:
- `counts_verified = true`
- `typed_values_match_source_oracle = true`
- `drivers_unique_and_join_safe = true`
- `gps_valid = true`
- `delta_readback = true`

Observed staging counts:
- `stg_trips = 144`
- `stg_drivers = 6`
- `stg_gps = 216`

The incremental Silver checks also all passed:
- `all_scenarios_match_independent_oracle = true`
- `business_keys_unique = true`
- `replay_preserves_business_content = true`
- `late_rows_retained = true`
- `actual_delta_files = true`

The fixed late batch retained:
- `SYN_LATE001` — Riyadh — 25.00 SAR
- `SYN_LATE002` — Jeddah — 27.00 SAR
- `SYN_LATE003` — Dammam — 29.00 SAR

Observed phases:
- base: 72 rows, 1794.60 SAR
- rerun: 72 rows, 1794.60 SAR
- late: 75 rows, 1875.60 SAR
- late replay: 75 rows, 1875.60 SAR

The dbt validation completed with `PASSED_DBT_NATIVE`.

## Decisions

The Silver grain is one row per `trip_id`, which is the business key. The lab's deterministic precedence orders candidates by `source_revision` descending and then stable source metadata (`_source_file`, `_source_sha256`, `_batch_id`) for deterministic selection. Conflicting records at the same revision are rejected instead of silently choosing one.

I kept the Bronze history intact and applied incremental changes to Silver rather than rebuilding unrelated source history. Replays are allowed at the Bronze delivery grain but must not multiply Silver business rows.

## Blockers / issues

No blocking error was observed. Staging, incremental Silver, late-arrival and dbt validation checks completed successfully.

## Evidence

Primary evidence is retained in the executed Day 2 notebook outputs and the resulting Delta Silver table.