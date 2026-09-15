# Governance Evidence

## Scope

This project uses only the fixed **synthetic Masar teaching dataset** supplied with the course. It is an engineering exercise for ingestion, quality, traceability and controlled downstream use. No real customer trips, phone numbers, identity numbers, credentials or real personal GPS traces are permitted in this repository.

This document records the governance design demonstrated by the project. It is not legal advice and it is not a compliance certificate.

## Dataset and ownership

- Dataset: `masar-small-v1` synthetic mobility fixture.
- Source location: `data/masar-small-v1/`.
- Purpose: learning and assessment for the SDA-DSC-214 data-engineering project.
- Data owner role: approves the intended purpose and permitted access.
- Operator role: runs the pipeline and investigates failures/recovery.
- Data steward role: explains data definitions, quality rules and defects.
- Consumer role: uses only approved/released snapshots.

For this training project, the learner can perform all four roles; the role separation documents responsibilities rather than claiming a staffed production organization.

## Lineage

### Trip lineage

`trips.csv` / `late_trips.csv` / correction fixtures
→ append-only Bronze Delta with source metadata
→ typed/normalized staging
→ validated and deduplicated Silver (one row per `trip_id`)
→ Delta correction/history controls
→ Great Expectations candidate validation
→ quarantine for rejected rows / approved 75-trip snapshot
→ Day 5 release
→ Gold reporting, BI facts/dimensions and AI feature/label products.

### Event lineage

`gps.ndjson` plus replay/late fixtures
→ Kafka producer
→ Kafka consumer / Spark Structured Streaming source
→ raw Delta event receipts with persistent checkpoint
→ unique event snapshot
→ aggregation by trip before the BI fact join
→ Gold/BI/AI downstream use.

The executed run ended with 217 unique events after the supplied late-event scenario. Raw coordinates are not exported in `bi.fact_trips`.

## Layer access intent

| Layer | Intended use | Access intent |
|---|---|---|
| Bronze | forensic/source-preserving landing | engineering/operator only; retain raw source evidence |
| Silver | trusted conformed business rows | engineering, quality and approved downstream builders |
| Quarantine | rejected records plus reasons | steward/operator diagnosis; not approved analytics input |
| Gold / BI | reconciled reporting products | reporting/analytics consumers |
| AI feature/label products | point-in-time training/analysis inputs | approved data-science consumers with documented cutoffs |

This is a design intent for the training project. The local lab does **not** implement a production identity and access management system.

## Implemented controls

The executed labs demonstrated:
- fixed synthetic inputs and source hashes;
- append-only Bronze delivery history;
- schema and relationship validation;
- deterministic Silver business-key handling;
- Delta transaction history and prior-version reads;
- rejection of invalid/conflicting writes;
- local Kafka transport with persistent Spark checkpointing;
- Great Expectations validation at the promotion boundary;
- quarantine with explicit rejection reasons;
- failure-safe release behaviour that preserves the previous approved release;
- point-in-time feature availability checks;
- no fabricated future labels;
- preserved notebook outputs and project documentation.

## Controls not implemented here

The following are production design concerns, not controls proven by this lab:
- user authentication;
- TLS for Kafka/network traffic;
- multi-user authorization;
- row-level access policies;
- enterprise secrets management;
- multi-broker Kafka resilience;
- statutory/organizational retention enforcement.

The Kafka broker was intentionally local/loopback for the exercise, and no credentials should be committed to Git.

## Quality and quarantine

The Day 4 quality candidate contained 82 rows: 75 valid trips and 7 fixed invalid cases. The invalid records were quarantined with reasons, while the 75-trip approved snapshot remained the only candidate eligible for downstream publication.

Observed quarantine reasons:
- `MISSING_TRIP_ID`
- `INVALID_FARE`
- `UNKNOWN_DRIVER`
- `INVALID_TIMESTAMP`
- `INVALID_DURATION`
- `INVALID_DISTANCE`
- `INVALID_CITY`

A failed candidate does not become trusted merely because some rows are valid; the gate blocks unsafe promotion.

## Retention and recovery

Learning evidence should be retained through project submission and the organizer's review period. The Kafka broker's 168-hour exercise setting is a training configuration, not an organizational or statutory retention rule. Kafka log retention and Delta version retention are separate mechanisms.

Failures must not be hidden by deleting checkpoints, volumes or reports. The course handoff archive moves the project workspace evidence between sessions; prior evidence should remain available for audit/review.

If this design were applied to real data, retention/erasure periods, access rules and privacy controls would have to be set by the responsible organization under its applicable policies and legal requirements.

## Release and consumer rule

Only an approved release/snapshot is consumed downstream. The integration lab proved that an injected failure did not overwrite the prior approved release. A recovery run received a new release identity only after validation and then reconciled to the same business contents.

## Programme context

Developed as part of **Modern Data Engineering for AI Systems (SDA-DSC-214)** at **SDAIA Academy**. The project data is synthetic and the governance record describes a teaching implementation, not a production compliance certification.

#SDAIAAcademy