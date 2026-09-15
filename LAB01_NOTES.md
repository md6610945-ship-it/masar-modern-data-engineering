# LAB01_NOTES.md

## Lab 01 — Land and Inspect Raw Feeds

### What I did
- Verified the fixed source manifest before processing. The manifest verification completed successfully for 10 files.
- Inspected the three base feeds: trips, drivers, and GPS events.
- Loaded the raw feeds into real Delta Bronze tables with source and ingestion metadata preserved.
- Ingested the base trip batch, driver batch, and GPS-event batch.
- Replayed the trip feed as a second delivery without overwriting the first Bronze snapshot.
- Retained the generated Bronze evidence in `reports/bronze.json`.

### What I observed
- Base source counts were:
  - Trips: 72 rows
  - Drivers: 6 rows
  - GPS events: 216 rows
- Base keys were unique before replay:
  - 0 duplicate trip-id groups
  - 0 duplicate driver-id groups
  - 0 duplicate event-id groups
- No missing top-level fields were observed in the three base feeds.
- Relationship checks passed:
  - 0 trips without a driver
  - 0 events without a trip
  - 0 GPS events with invalid coordinates
- City labels were not fully consistent in the raw trips. Labels included values such as `" riyadh "`, `" jeddah "`, and `" dammam "`. Normalization would change 10 rows. This is an observed source-data defect, so it should be corrected downstream rather than rewriting Bronze.
- After the intentional trip replay, Bronze contained 144 trip deliveries while the number of distinct business trips remained 72.
- Spark version used by the successful run: 3.5.8.

### Quality risks
1. **Replay / duplicate-delivery risk:** the same logical trip can arrive more than once. The replay demonstrated this directly: Bronze grew from 72 to 144 trip deliveries while there were still only 72 distinct trip ids.
2. **Type-conversion risk:** raw trip CSV fields were read as strings, including timestamps, fare, and distance. Invalid or inconsistent values could fail or change meaning when typed later in Silver.
3. **Reference and location-integrity risk:** trip-to-driver links, event-to-trip links, and GPS coordinates must continue to be validated for later or changed inputs. The base data had no violations, but these checks remain required because future deliveries could contain them.

### Decisions
- Bronze is append-only and preserves delivery history rather than deduplicating or cleaning source records.
- Raw payloads and source hashes are preserved so downstream tables can be rebuilt and audited.
- City-label normalization is deferred to Silver because Bronze should preserve what arrived.
- Business-level deduplication is also deferred to Silver; repeated Bronze deliveries are evidence, not records to delete from Bronze.

### Evidence retained
- `source_inspection.json`
- `reports/bronze.json`
- Executed Lab 01 notebook outputs
- Successful Bronze workspace referenced by `outputs/day01_bronze_success.json`

### Problems / blockers
No blocker remained at the end of the successful Lab 01 run. All recorded source-inspection and Bronze verification checks passed.
