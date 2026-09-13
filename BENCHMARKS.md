# BENCHMARKS

## Purpose
Record the assumptions and observed scan evidence used in Day 1. The dataset is synthetic and intentionally small, so these measurements are learning evidence rather than production benchmarks.

## Cost assumptions
The cost model uses hypothetical teaching units only. It does not represent SAR, USD, or any provider's current pricing.

## Operating models

### Always-on
Keeps compute available for the whole operating period. This avoids startup delay but includes idle compute time.

### Scheduled
Starts compute when the workload needs it and stops it afterwards. This can reduce idle time for intermittent batch workloads but introduces startup and shutdown overhead.

### Counterexample
If a workload is continuous, very frequent, or latency-sensitive, scheduled start/stop behavior may be worse than keeping compute available.

## Spark scan evidence
Environment expected by the Day 1 notebook:
- Python 3.11
- PySpark 3.5.8
- delta-spark 3.3.3
- Java 17
- Synthetic Masar trips dataset

Repository verification confirms that the Day 1 scan exercise produced positive measured samples, saved query plans, equal source populations, and results matching the expected oracle.

The exact wall-clock values should be read from the executed Day 1 notebook from the learner run. They are intentionally not invented here.

## Interpretation
Any timing difference on 72 base trips is only an observation from that run. It does not establish a general speed advantage.

Potential influences include:
- Spark/JVM startup,
- caching,
- notebook host load,
- tiny input size,
- repeated-run warm-up effects.

## Limitations
These are not production-scale performance benchmarks. A meaningful performance claim would require larger data, repeated controlled trials, and a stable benchmark environment.
