# LAB02 Notes — Cost and Local Scan Evidence

## Objective
Use explicit assumptions to compare compute operating models, then collect local Spark scan evidence with equal-result checks and saved query plans.

## Cost model
All cost values in this lab are hypothetical teaching units, not SAR, USD, or a real cloud-provider price list.

### Always-on compute
Advantages:
- Immediately available.
- No startup wait for scheduled jobs.

Disadvantages:
- Idle time still consumes compute resources.
- Inefficient for intermittent workloads.

### Scheduled compute
Advantages:
- Reduces idle compute for predictable batch workloads.
- Compute can be stopped when not needed.

Disadvantages:
- Startup/shutdown add overhead.
- Jobs may wait for resources to become ready.

### Counterexample
Scheduled operation is not always cheaper or better. For continuous, very frequent, or latency-sensitive workloads, repeated startup overhead can outweigh the benefit of stopping compute.

## Spark scan evidence
The Day 1 verification record confirms:
- equal source populations,
- positive measured samples,
- saved query plans,
- query results matching the expected oracle.

The executed notebook is the primary evidence for the exact run output. I do not claim a general speed-up from this tiny dataset.

## Interpretation
The dataset is intentionally small, so observed timings are local measurements only. JVM warm-up, Spark startup, caching, and notebook load can materially affect them.

## Limitations
- Only 72 base trips are used.
- The notebook environment is a teaching environment, not a production benchmark platform.
- Timing differences do not prove that one query form is universally faster.

## Result
Lab 02 demonstrates compute-storage reasoning with explicit assumptions and measured Spark evidence while keeping the performance claims bounded.
