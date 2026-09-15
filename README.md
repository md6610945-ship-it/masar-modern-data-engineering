# Masar Mini-Lakehouse — Mohammed Al-Ahmari

A complete five-day mini-lakehouse project that turns a fixed **synthetic** mobility dataset into reliable Bronze, Silver and Gold/serving products. The pipeline preserves source history, builds one trusted trip definition, handles Delta corrections and recovery, ingests Kafka events with persistent checkpoints, blocks unsafe promotion with Great Expectations, and serves reconciled BI tables plus point-in-time AI feature/label tables.

## Programme

This project was developed as part of **Modern Data Engineering for AI Systems (SDA-DSC-214)** at **SDAIA Academy** — https://github.com/SDAIAAcademy.

#SDAIAAcademy

## What this project demonstrates

The eight cumulative labs are one project:

1. Land and inspect raw feeds in append-only Delta Bronze.
2. Compare compute/storage assumptions and record local scan evidence.
3. Build typed, normalized, driver-validated and idempotent Silver.
4. Exercise Delta schema enforcement, corrections, history and safe recovery.
5. Stream GPS events through Kafka and Spark with persistent checkpoints.
6. Gate promotion with Great Expectations and quarantine invalid records.
7. Integrate the dependency-ordered pipeline with failure-safe releases.
8. Serve Gold, BI and AI outputs from one trusted lineage.

## Architecture

```text
Synthetic CSV / NDJSON inputs
        |
        v
Bronze Delta (append-only receipts + source metadata)
        |
        +---------------- Kafka / Spark streaming ----------------+
        |                                                         |
        v                                                         v
Staging / Silver trips                                 Unique event snapshot
(typed, conformed, validated,                                  |
deduplicated, corrected)                                       |
        |                                                       |
        +---------------- Quality gate --------------------------+
                            |
                  +---------+---------+
                  |                   |
                  v                   v
              Quarantine        Approved snapshot
                                      |
                              Controlled release
                                      |
                     +----------------+----------------+
                     |                                 |
                     v                                 v
                Gold / BI                         AI features
          reporting products                  and aligned labels
```

### Layer guarantees

- **Bronze:** append-only delivery history with source filename/hash and ingestion metadata; no business normalization.
- **Silver:** one trusted row per `trip_id`, normalized/typed values, validated driver relationship and deterministic replay handling.
- **Quality boundary:** invalid candidate rows are quarantined with reasons and failed candidates are not promoted.
- **Gold / BI:** explicit grains, valid dimension relationships and reconciled trip/fare totals.
- **AI:** explicit as-of/prediction keys; features exclude information unavailable at the cutoff and unavailable future labels remain null.

## Data

The repository uses the supplied fictional Masar dataset only. Base inputs contain:

- 72 trips
- 6 drivers
- 216 GPS events

The intentional Bronze replay produces 144 trip **delivery receipts** while still representing 72 distinct base business trips. The fixed late-trip batch takes trusted Silver to 75 trips. The streaming replay/late scenario finishes with 217 unique events.

No real personal records or real customer GPS traces are part of this project.

## Environment verified

The executed notebook recorded:

- Google Colab / Linux teaching runtime
- Python 3.11.13
- OpenJDK 17.0.20
- PySpark 3.5.8
- delta-spark 3.3.3
- Great Expectations 1.7.0
- Kafka 4.0.2 (local teaching broker)
- kafka-python 2.2.15

The course requirement files and setup guidance are retained in this repository.

## How to run

Clone this fork and use the `develop` branch:

```bash
git clone https://github.com/md6610945-ship-it/masar-modern-data-engineering.git
cd masar-modern-data-engineering
git switch develop
```

Follow the daily notebooks in dependency order and do not regenerate an unrelated fresh dataset between days:

```text
day01/STUDENT.ipynb
day02/STUDENT.ipynb
day03/STUDENT.ipynb
day04/STUDENT.ipynb
day05/STUDENT.ipynb
```

Each day consumes the previous day's accumulated lakehouse state. The committed notebooks retain the learner's executed outputs as evidence.

## Results

### Bronze

- Base: 72 trips, 6 drivers, 216 GPS events.
- Intentional trip replay: 144 Bronze trip receipts, 72 distinct business trips.
- Source hashes/raw evidence and Delta files preserved.

### Silver

| Phase | Trusted trips | Fare total (SAR) |
|---|---:|---:|
| Base | 72 | 1794.60 |
| Same-input rerun | 72 | 1794.60 |
| Late batch | 75 | 1875.60 |
| Late-batch replay | 75 | 1875.60 |

The reruns show idempotent business content while the fixed late delivery adds exactly three trips.

### Delta reliability

- Schema-invalid writes were rejected.
- Same-revision conflicts were rejected.
- Mixed valid/invalid writes were rejected atomically.
- Earlier Delta versions were read successfully.
- Destructive recovery exercises were isolated from trusted Silver.
- Recovery-copy delete/restore changed 75 → 74 → 75 while trusted Silver remained unchanged.

### Streaming

| Phase | Transport receipts | Unique event IDs |
|---|---:|---:|
| Base | 216 | 216 |
| Restart | 216 | 216 |
| Duplicate replay | 218 | 216 |
| Late event | 219 | 217 |

The same persistent checkpoint was reused for the logical query. Duplicate transport deliveries did not multiply the unique event set; the supplied late event was retained.

### Quality gate

The mixed quality candidate contained 82 rows: 75 valid + 7 fixed invalid cases. Great Expectations rejected the mixed candidate, quarantined all seven bad records with reasons and preserved the 75-row approved snapshot. The failed candidate was not promoted.

### BI reconciliation

| Zone | Trips | Fare (SAR) |
|---|---:|---:|
| Dammam | 25 | 670.40 |
| Jeddah | 25 | 625.20 |
| Riyadh | 25 | 585.00 |
| **Total** | **75** | **1880.60** |

Trusted release versus BI output:

- trip difference = **0**
- fare difference = **0.00 SAR**

The BI fact has one row per trip, valid foreign keys and aggregates events before the join. It does not export raw GPS coordinates.

### AI availability

The feature table uses an explicit as-of cutoff and prediction hour. A saved Dammam example used `2026-06-04T03:05:00Z` as the cutoff, with eight eligible completed trips in the prior 24 hours and no source availability after the cutoff. The corresponding future target was not available and remained null with `label_status = UNOBSERVED` rather than being fabricated.

## Data-product grains

- `gold.zone_hourly_demand`: `zone_key + hour_utc`
- `gold.driver_daily`: `driver_key + trip_date_local`
- `bi.dim_zone`: one row per `zone_key`
- `bi.dim_driver`: one row per `driver_key`
- `bi.dim_date`: one row per `date_key`
- `bi.fact_trips`: one row per `trip_id`
- `ai.zone_hourly_features`: `zone_key + as_of_utc + prediction_hour_utc`
- `ai.zone_hourly_labels`: aligned prediction key plus label availability/status

## Documentation

- [LAB01_NOTES.md](LAB01_NOTES.md) through [LAB08_NOTES.md](LAB08_NOTES.md): observed evidence for every lab.
- [BENCHMARKS.md](BENCHMARKS.md): cost assumptions, measurement contract, timings and limits.
- [GOVERNANCE.md](GOVERNANCE.md): lineage, ownership roles, intended access, quality and retention boundaries.
- [DECISIONS.md](DECISIONS.md): architecture, performance, reliability and governance decisions with trade-offs.
- [day02/DATA_CONTRACT.md](day02/DATA_CONTRACT.md): Silver contract and grain.
- [day05/DATA_PRODUCTS.md](day05/DATA_PRODUCTS.md): Gold/BI/AI product contracts.

## Key decisions

The most important choices are documented in `DECISIONS.md`:

1. Keep Bronze append-only instead of overwriting repeated deliveries.
2. Use `trip_id` as the Silver business key with deterministic precedence and explicit conflict rejection.
3. Block unsafe promotion at the quality/release boundaries and preserve the previous approved release after failure.
4. Keep BI and AI as separate products from one trusted lineage, with point-in-time availability for AI.

## Limitations

- The dataset is deliberately small and synthetic; 72 base trips are not a production-scale benchmark.
- The timing measurements are local/Colab observations and do not prove general file-format performance or cloud cost.
- Teaching units in the cost model are not real currency.
- Kafka is a local teaching broker without production authentication, TLS or multi-broker resilience.
- The governance document describes a training design and is not a compliance certification.
- `zone_key` is a city-level proxy, not a real service-zone or neighbourhood boundary.
- The AI tables demonstrate point-in-time feature/label handling; this project does not train or deploy a production prediction model.

## Credits

Course materials and original Masar learning repository by **Meaad Al-Marri**.

Developed as part of **Modern Data Engineering for AI Systems (SDA-DSC-214)** at **SDAIA Academy** — https://github.com/SDAIAAcademy.

#SDAIAAcademy
