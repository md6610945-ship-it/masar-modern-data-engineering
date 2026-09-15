# LAB05 Notes — Stream Events and Resume Safely

## What I did

I ran the supplied local Kafka environment and used an actual producer/consumer together with a Spark Structured Streaming source. The streaming query used a persistent checkpoint, then I exercised the base delivery, a stop/restart, duplicate event delivery and a late event.

## What I observed

The local Kafka broker became ready on `127.0.0.1:9092`. The executed environment reported Kafka 4.0.2 and `kafka-python` 2.2.15.

All saved streaming checks passed:
- `phase_transport_counts = true`
- `phase_event_counts = true`
- `transport_keys_always_unique = true`
- `restart_same_query_identity = true`
- `restart_new_execution_ids = true`
- `checkpoint_same_for_all_phases = true`
- `actual_checkpoint_files_present = true`
- `producer_consumer_offsets_reconcile = true`
- `source_json_text_preserved = true`
- `event_content_matches_source = true`
- `unique_events_delta_readback = true`
- `all_events_link_to_trusted_trips = true`
- `late_event_retained = true`

Observed transport-receipt counts by phase were:
- 216
- 216
- 218
- 219

Observed unique event-ID counts by phase were:
- 216
- 216
- 216
- 217

The stop/restart preserved the same checkpoint/query state without multiplying the unique event set. The replay increased transport receipts but the unique business-event count remained 216. The late delivery added one new unique event, taking the unique-event count to 217, and it was retained.

## Event time and processing time

`event_ts` represents the event time carried by the source event. Processing time is when Spark/Kafka receives and handles that event. A delayed event can therefore have an older event time than its processing time. In this lab the supplied late event was retained and evidenced explicitly; I do not generalize this local exercise into a claim about all production late-event policies.

## Decisions

Each streaming query must have its own persistent checkpoint, and a restart must reuse the intended checkpoint when resuming the same logical query. Business-event uniqueness is evaluated by event identity rather than by counting Kafka transport receipts.

## Limitations

This was a local teaching broker. It did not implement production authentication, TLS, multi-broker resilience or a production-scale delivery environment, so the result demonstrates the lab's recovery pattern rather than proving distributed production Kafka semantics.

## Blockers / issues

No blocking error remained. The base, restart, replay and late-event phases completed successfully with the saved evidence.

## Evidence

Primary evidence is retained in the executed Day 4 notebook outputs and the generated streaming report/checkpoint artifacts in the runtime workspace.