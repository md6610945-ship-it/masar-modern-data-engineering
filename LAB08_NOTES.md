# LAB08 Notes — Serve AI and BI

## What I did

I produced the Gold, BI and AI serving outputs from the same trusted lineage. I validated explicit table grains and keys, checked BI foreign keys and reconciliation, aggregated events before joining to trip facts, and verified point-in-time availability for AI features without fabricating an unavailable future label.

## What I observed

All saved serving checks passed:
- `gold.zone_hourly_demand_schema_and_keys = true`
- `gold.driver_daily_schema_and_keys = true`
- `bi.dim_zone_schema_and_keys = true`
- `bi.dim_driver_schema_and_keys = true`
- `bi.dim_date_schema_and_keys = true`
- `bi.fact_trips_schema_and_keys = true`
- `ai.zone_hourly_features_schema_and_keys = true`
- `ai.zone_hourly_labels_schema_and_keys = true`
- `fact_grain_75 = true`
- `foreign_keys_valid = true`
- `gold_fact_totals_match = true`
- `events_aggregated_before_join = true`
- `group_grains_reconcile = true`
- `labels_not_fabricated = true`
- `feature_availability_checked = true`
- `feature_label_keys_aligned = true`

### Output grains

- `gold.zone_hourly_demand`: one observed city proxy and UTC trip-start hour; key `zone_key + hour_utc`.
- `gold.driver_daily`: one driver and local trip-start date; key `driver_key + trip_date_local`.
- `bi.dim_zone`: one unique `zone_key`.
- `bi.dim_driver`: one unique `driver_key`.
- `bi.dim_date`: one local date keyed by `date_key`.
- `bi.fact_trips`: one unique `trip_id`.
- `ai.zone_hourly_features`: one `zone_key + as_of_utc + prediction_hour_utc`.
- `ai.zone_hourly_labels`: the aligned prediction key with label availability/status fields.

### BI reconciliation

Observed zone totals were:
- Dammam: 25 trips, 670.40 SAR
- Jeddah: 25 trips, 625.20 SAR
- Riyadh: 25 trips, 585.00 SAR

Reconciled total:
- trusted/BI trips: 75 vs 75 → difference 0
- trusted/BI fare: 1880.60 SAR vs 1880.60 SAR → difference 0.00 SAR

The BI fact table retained 75 trips and used 217 unique GPS events after aggregating events before the join. Raw coordinates were not exported in the BI fact table.

### AI point-in-time evidence

One saved feature example used Dammam with `as_of_utc = 2026-06-04T03:05:00Z` and prediction hour `2026-06-04T04:00:00Z`. Its 24-hour history contained 8 completed trips with an average duration of 1470 seconds, and the maximum source-availability time was not after the cutoff.

The corresponding future label was intentionally unavailable in the fixture: `target_trip_count` remained null with `label_status = UNOBSERVED`. This is evidence that the pipeline did not invent a future label.

## Decisions

BI and AI are separate data products from one trusted lineage. BI receives reconciled dimensions/facts without raw coordinate export, while AI features use explicit as-of/prediction cutoffs so information unavailable at prediction time is excluded.

## Limitations

The zone definition is a city-level proxy, not a real operational service-zone boundary. The dataset is small and synthetic, and the fixed feature/label exercise is evidence of point-in-time logic rather than a production model or live demand forecast.

## Blockers / issues

No blocking error remained. All saved Gold, BI, AI, reconciliation and feature-availability checks passed.

## Evidence

Primary evidence is retained in the executed Day 5 notebook outputs and generated serving/release evidence.