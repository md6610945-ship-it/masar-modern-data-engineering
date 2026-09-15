# Benchmark Evidence

This file records the cost assumptions and scan measurements observed in the executed project notebook. Hypothetical cost-model units are kept separate from measured elapsed time.

## 1. Cost-model assumptions

The course cost model uses **teaching units (TU)** only. TU is not SAR, USD or a quoted cloud price.

| Assumption | Value |
|---|---:|
| Days per month | 30 |
| Hours per day | 24 |
| Cores | 4 |
| Compute price | 0.50 TU / core-hour |
| Storage | 100 GiB |
| Storage price | 0.20 TU / GiB-month |
| Scheduled work | 2 h/day |
| Startup overhead | 0.25 h/day |
| Scheduled monthly fixed overhead | 30 TU |
| Always-on monthly fixed overhead | 0 TU |

## 2. Cost-model results

| Measure | Always-on | Scheduled |
|---|---:|---:|
| Runtime hours/month | 720.00 | 67.50 |
| Compute TU | 1440.00 | 135.00 |
| Storage TU | 20.00 | 20.00 |
| Fixed overhead TU | 0.00 | 30.00 |
| Total TU | **1460.00** | **185.00** |

Under only these stated assumptions, scheduled operation is 1275 TU lower (about 87.33%). The calculated break-even workload is **23.25 work hours/day**.

### Counterexample

At **23.75 work hours/day**, scheduled operation becomes **1490 TU**, which is **30 TU more** than the 1460 TU always-on case. This is why the project does not claim that scheduled compute is always cheaper.

## 3. Spark scan measurement contract

- Input: the fixed synthetic `trips.csv` / equivalent Delta version-0 data.
- Business result checked for equality before timing comparison.
- Result: 72 rows, 72 non-null fares, total fare 1794.60 SAR.
- Environment observed in the notebook: Python 3.11.13, Java 17.0.20, PySpark 3.5.8, Delta Lake (`delta-spark`) 3.3.3 in Google Colab/local teaching execution.
- One warm-up was performed for each path before recorded samples.
- Four recorded repetitions per path.
- The comparison included the same aggregate business result.
- Query plans were saved by the lab for both CSV and Delta reads.
- Cache effects from the OS/JVM/metadata layer cannot be excluded on such a small benchmark.

## 4. Observed elapsed times

### CSV

Recorded seconds:

`0.204056404, 0.273864178, 0.200422811, 0.388999725`

- Minimum: 0.200422811 s
- Median: **0.238960291 s**
- Maximum: 0.388999725 s

### Delta version 0

Recorded seconds:

`1.329042936, 1.373393722, 1.306951148, 1.760607276`

- Minimum: 1.306951148 s
- Median: **1.351218329 s**
- Maximum: 1.760607276 s

## 5. Interpretation

The two scans returned equal business results. In this specific tiny local run, the recorded CSV samples were lower than the Delta samples. That observation **does not establish a general performance ranking**. Delta provides transaction, schema and versioning capabilities exercised elsewhere in this project; this micro-benchmark is too small to demonstrate production throughput or scale.

## 6. Limits

These measurements cannot establish:
- real cloud cost;
- production scalability;
- guaranteed speed-up for either file format;
- distributed-cluster behaviour;
- steady-state performance under large data volumes.

Session startup, JVM state, filesystem cache and Delta metadata overhead can dominate a 72-row experiment. The cost values above are teaching assumptions and must not be presented as real currency or provider pricing.

## Evidence locations

- Executed Day 1 notebook cells and retained outputs.
- Lab-generated benchmark report and query plans in the original runtime workspace.
- `LAB02_NOTES.md` for the summarized observation and interpretation.