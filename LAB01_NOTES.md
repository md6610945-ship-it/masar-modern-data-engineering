# LAB01 Notes — Land and Inspect Raw Feeds

## Objective
Inspect the fixed source files, verify the manifest before processing, and land the raw feeds into an append-only Delta Bronze layer.

## Source inspection
The project uses synthetic data only.

Observed base counts:
- Trips: 72
- Drivers: 6
- GPS events: 216

The source manifest was verified before processing.

## Bronze design
Bronze is append-only. Raw values and source/ingestion metadata are preserved. Business cleaning, deduplication, timestamp normalization, label conformance, and driver validation are deferred to Silver.

## Replay evidence
The trips feed is intentionally delivered twice. The expected Bronze result is 144 physical trip rows representing 72 distinct business trips. This is intentional history preservation.

## Observed facts
- The three feeds arrive in different formats.
- The base dataset contains 72 trips, 6 drivers, and 216 GPS events.
- Replaying trips increases the Bronze row count because Bronze is append-only.
- Repository verification confirms real Delta files, preserved payload/source hashes, and two trip deliveries.

## Quality risks
1. Duplicate delivery of the same trip batch.
2. Late-arriving batches.
3. Unexpected source schema changes.
4. Corrections arriving after the original record.

## Decision
Keep Bronze immutable and append-only. Handle deduplication and business conformance in Silver with a documented business key and deterministic precedence rule.

## Result
The source inspection and Bronze stage provide the raw historical foundation for the later Silver and Gold layers.
