# Architecture Decision Log

This log records the significant choices demonstrated by the executed Masar Mini-Lakehouse project. Each decision links the choice to observed evidence and states the trade-off rather than repeating implementation code.

## D1 — Keep Bronze append-only and source-faithful

**Decision:** Preserve each delivery as a separate Bronze receipt with source filename, SHA-256, batch identity and ingestion metadata. Do not normalize business values in Bronze.

**Alternative considered:** overwrite/rewrite the raw landing table when the same business trip is delivered again.

**Reason and evidence:** the intentional trip replay increased Bronze trip receipts from 72 to 144 while the number of distinct business trips remained 72. Keeping both deliveries preserved the history needed to prove replay behaviour.

**Consequences:** Bronze can contain duplicate business identities by design, so downstream layers must not use Bronze row count as business-trip count. Storage grows with deliveries, but rebuild/audit capability is preserved.

## D2 — Treat cost claims as assumptions, not provider pricing

**Decision:** Use the course teaching-unit model to compare scheduled and always-on operation, and state every assumption separately from measured Spark timings.

**Alternative considered:** claim scheduled compute is always cheaper or translate teaching units into real currency.

**Reason and evidence:** under the base assumptions scheduled cost was 185 TU versus 1460 TU always-on, but at 23.75 work hours/day scheduled became 1490 TU, 30 TU higher than always-on. The break-even point was 23.25 work hours/day.

**Consequences:** the result is useful for reasoning about compute/storage separation but cannot be presented as real cloud pricing. Changes in workload, startup overhead or provider pricing can reverse the decision.

## D3 — Make `trip_id` the Silver business key with deterministic precedence

**Decision:** Silver has one row per `trip_id`. Candidate precedence uses `source_revision` descending followed by stable source metadata (`_source_file`, `_source_sha256`, `_batch_id`) for deterministic selection. Same-revision payload conflicts are rejected.

**Alternative considered:** keep all delivery rows in Silver or select a row by nondeterministic arrival/order behaviour.

**Reason and evidence:** base and rerun both produced 72 trusted rows and 1794.60 SAR; the late batch produced 75 rows and 1875.60 SAR, and replaying it still produced 75. The uniqueness and replay-preservation checks passed.

**Consequences:** Silver provides one trusted definition of a trip and is idempotent for replays. Explicit correction/version rules are required when business content genuinely changes.

## D4 — Reject unsafe Delta writes and isolate destructive maintenance

**Decision:** rely on schema enforcement, transaction history and explicit correction semantics. Run delete/restore/maintenance exercises only on isolated training copies.

**Alternative considered:** silently coerce/drop invalid data or run destructive experiments directly on the trusted Silver table.

**Reason and evidence:** invalid schema, same-revision conflict and mixed valid/invalid batch tests were rejected. The trusted table stayed at 75 rows. On a recovery copy, a delete moved 75→74 and restore returned 74→75 with the original business digest, while trusted Silver remained unchanged.

**Consequences:** safety and auditability take priority over convenience. Maintenance requires more deliberate paths/copies, but a training or operator mistake does not modify the trusted table.

## D5 — Separate Kafka transport receipts from business-event identity

**Decision:** maintain a persistent checkpoint for the streaming query and deduplicate/reconcile by event identity rather than treating every transport receipt as a new business event.

**Alternative considered:** count Kafka receipts directly as unique events or restart without preserving query checkpoint state.

**Reason and evidence:** transport counts moved 216 → 216 → 218 → 219 across the base/restart/replay/late phases, while unique event IDs moved 216 → 216 → 216 → 217. Restart/checkpoint and offset reconciliation checks passed.

**Consequences:** duplicate deliveries can be observed without double-counting the business event set. This local single-broker lab does not prove production distributed Kafka delivery semantics and would need authentication/TLS/resilience controls in production.

## D6 — Block promotion when quality fails; quarantine with reasons

**Decision:** run Great Expectations at the promotion boundary. Preserve rejected rows with explicit reasons and publish only a fully validated snapshot.

**Alternative considered:** drop bad rows silently and publish the remainder of an otherwise failed candidate.

**Reason and evidence:** the 82-row mixed candidate contained 75 valid and 7 invalid rows. It failed the gate; seven rows were quarantined with explicit reasons and the approved snapshot remained 75 rows. `failed_candidate_not_promoted` and source-Silver-preservation checks passed.

**Consequences:** downstream users receive a clear approved snapshot while operators retain diagnostic evidence. Quality-rule changes require review because a threshold change can alter what is publishable.

## D7 — Publish releases only after dependency checks pass

**Decision:** run Bronze → Silver → Gold/serving in dependency order and treat publication as a controlled release. Preserve the previous approved release if a new run fails.

**Alternative considered:** overwrite downstream output progressively during a run.

**Reason and evidence:** the Day 5 injected-failure test was observed, the previous release remained intact, and the recovery build produced a new identity while matching the same logical business content.

**Consequences:** release identity can change while business contents stay equal, so reconciliation must compare stable contents rather than volatile timestamps/run IDs. This adds a release step but prevents partial unsafe publication.

## D8 — Serve BI and AI as separate products from one lineage

**Decision:** derive BI and AI outputs from the same trusted release but give each an explicit grain/contract. Aggregate GPS events before joining to the trip fact, do not export raw coordinates in the BI fact, and enforce as-of cutoffs for AI features.

**Alternative considered:** expose one wide table containing raw trip/event fields to both BI and model consumers.

**Reason and evidence:** BI facts reconciled to 75 trips and 1880.60 SAR with zero difference, foreign keys passed, 217 unique events were aggregated before the trip join, and the AI feature-availability checks passed. An unavailable future label stayed null with `label_status = UNOBSERVED` rather than being fabricated.

**Consequences:** the products are easier to reason about and safer for their intended consumers, but their grains and availability rules must be documented. The zone is only a city proxy and the dataset is synthetic, so these products are not production demand models.

## Conditions that would change these decisions

These choices would need re-evaluation for real data, concurrent multi-writer ingestion, changing source contracts, large-scale/distributed Kafka, enterprise access control, real cloud pricing, stricter latency objectives or legal retention/privacy obligations.