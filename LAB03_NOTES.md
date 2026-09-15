# LAB03 Notes — Build Incremental Silver

## What I did

I prepared typed staging tables from the existing Bronze data and then built the incremental Silver trip table. The staging run validated the trip, driver and GPS inputs before publication. I then ran the incremental Silver flow, including deduplication, late-arrival handling and rerun checks. I also ran the dbt version of the transformation graph and generated documentation evidence.

## What I observed

The staging checks all passed:
- `counts_verified = true`
- `typed_values_match_source_oracle = true`
- `drivers_unique_and_join_safe = true`
- `gps_valid = true`
- `delta_readback = true`

Observed staging row counts were:
- `stg_trips = 144`
- `stg_drivers = 6`
- `stg_gps = 216`

The incremental Silver checks also all passed:
- `all_scenarios_match_independent_oracle = true`
- `business_keys_unique = true`
- `replay_preserves_business_content = true`
- `late_rows_retained = true`
- `actual_delta_files = true`

The Silver output retained the three late trips:
- `SYN_LATE001` — Riyadh — 25.00 SAR
- `SYN_LATE002` — Jeddah — 27.00 SAR
- `SYN_LATE003` — Dammam — 29.00 SAR

The dbt validation finished with `PASSED_DBT_NATIVE`. The observed phases were:
- base: 72 rows, 1794.60 SAR
- rerun: 72 rows, 1794.60 SAR
- late: 75 rows, 1875.60 SAR
- late_replay: 75 rows, 1875.60 SAR

This showed that rerunning the same logical input did not duplicate business rows, while the fixed late batch increased the trusted trip table from 72 to 75 rows.

## Decisions

I treated the Silver layer as the single trusted definition of a trip after typing, normalization, relationship validation and deterministic deduplication. I preserved the Bronze source history and applied incremental changes to Silver instead of rebuilding unrelated data.

For correctness, I relied on the documented business key and deterministic precedence rule used by the lab implementation. I also verified idempotency by comparing the base/rerun and late/late_replay results.

## Blockers / issues

No blocking error was observed in this lab. The staging, incremental Silver and dbt validation runs completed successfully in the saved notebook outputs.

## Evidence

Primary evidence is retained in the executed notebook outputs for Day 2, including the staging checks, incremental Silver checks and dbt validation report.