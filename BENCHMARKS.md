# BENCHMARKS

## Purpose
Record the assumptions and observed Day 1 measurements from my executed notebook. The dataset is synthetic and intentionally small, so these measurements are learning evidence rather than production benchmarks.

## Environment actually used
- Google Colab runtime
- Python 3.11.13
- PySpark 3.5.8
- delta-spark 3.3.3
- Java 17
- Synthetic Masar dataset: 72 base trips

## Cost assumptions
The cost model uses hypothetical teaching units (TU) only. These values are not SAR, USD, or any cloud provider's current pricing.

## Cost-model results
- Always-on total: **1460.00 TU**
- Scheduled total: **185.0000 TU**
- Difference: **1275.00 TU** in this teaching scenario
- Break-even work hours per day: **23.25 h/day**

### Counterexample
The notebook also tested a workload above the break-even point. At **23.75 h/day**, scheduled operation became **30 TU more expensive** than always-on operation. This demonstrates that scheduled compute is not universally cheaper; startup/operation overhead and workload duration matter.

## Spark scan experiment
The benchmark compared logically equal-result scans of the same trip population using the source CSV and Delta version 0. One warm-up was used for each path, followed by four measured samples in balanced order. Session startup and ingestion were outside the measured interval.

### Equal-result aggregate
- Rows: **72**
- Non-null fares: **72**
- Fare total: **1794.60 SAR**

### CSV scan samples
- 0.10890634799943655 s
- 0.11094238599980599 s
- 0.11681015100020886 s
- 0.1130359579992728 s

CSV summary:
- Median: **0.1119891719995394 s**
- Minimum: **0.10890634799943655 s**
- Maximum: **0.11681015100020886 s**

### Delta version 0 scan samples
- 1.0241692199997487 s
- 0.7923787099998663 s
- 0.7902295410003717 s
- 0.9060651780000626 s

Delta summary:
- Median: **0.8492219439999644 s**
- Minimum: **0.7902295410003717 s**
- Maximum: **1.0241692199997487 s**

## Query-plan evidence
The executed notebook saved both actual plans:
- `reports/plans/csv.txt`
- `reports/plans/delta_v0.txt`

It also retained the benchmark evidence as `reports/benchmark.json` inside the Day 1 workspace.

## Interpretation
The CSV happened to be faster in this tiny local run, but that does **not** establish a general performance advantage. The dataset contains only 72 trips, and measurements can be strongly affected by JVM/Spark warm-up, OS and metadata caches, notebook host load, Delta overhead, and the very small input size.

## Limitations
These values are observations from this specific learner run, not production-scale benchmarks. A general performance claim would require larger data, repeated controlled trials, and a stable benchmark environment.
