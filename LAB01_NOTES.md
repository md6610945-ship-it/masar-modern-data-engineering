# LAB01 Notes — Land and Inspect Raw Feeds

## What I did

I verified the supplied source manifest before processing and inspected the three fixed synthetic feeds: trips, drivers and GPS events. The manifest verification covered 10 supplied files. I then loaded the raw feeds into real Delta Bronze tables while preserving source and ingestion metadata, and replayed the trip delivery without overwriting the earlier Bronze history.

## What I observed

The inspected base inputs contained:
- 72 trips
- 6 drivers
- 216 GPS events

The base relationship and coordinate checks passed. The city labels were semantically the same three cities but were not consistently formatted; 10 trip rows changed when the labels were trimmed/case-normalized for inspection.

After the intentional trip replay, Bronze contained:
- 144 trip receipts
- 6 driver rows
- 216 GPS event receipts
- 72 distinct business trips

All saved Bronze checks passed, including expected totals, two trip deliveries, preservation of the initial snapshot, preservation of source hashes/raw payloads and presence of real Delta files.

## Quality risks

1. **Observed defect:** city strings can differ by whitespace/case, so downstream conformance must normalize them before comparison or grouping.
2. **Observed delivery behaviour:** replaying the same trip file creates another valid Bronze receipt. A downstream table that does not deduplicate on the business key could double-count trips.
3. **Scenario risk:** late deliveries, corrections and schema changes can arrive later in the pipeline; Bronze must preserve enough source evidence to rebuild and diagnose them rather than silently overwrite history.

## Decisions

I kept Bronze append-only and byte/source faithful. I did not apply business normalization in Bronze. Source filename, source SHA-256, batch identity and ingestion metadata were retained so later layers can distinguish business identity from delivery history.

## Blockers / issues

No blocking error was observed. The Lab 01 source-inspection and Bronze validation checks completed successfully in the executed notebook.

## Evidence

Primary evidence is retained in the executed Day 1 notebook outputs. The lab also produced the Bronze machine report in the runtime workspace under `reports/bronze.json`.