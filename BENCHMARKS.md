# Benchmark Evidence

This file records the cost assumptions and Spark scan measurements actually observed during the project. Hypothetical cost-model units are kept separate from elapsed-time measurements, and repeated executions are reported as repeated executions rather than forced into one artificial number.

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

- Input: the fixed synthetic `trips.csv` and the equivalent Delta version-0 population.
- Business result checked for equality before timing comparison.
- Result: 72 rows, 72 non-null fares, total fare 1794.60 SAR.
- Spark version observed: 3.5.8.
- One warm-up per variant before four recorded repetitions.
- Same aggregate business work on CSV and Delta.
- Physical query plans retained under `reports/plans/`.
- No explicit Spark cache; OS/JVM/filesystem/metadata caches were not controlled.
- Timing scope excludes session startup and Bronze ingestion.

## 4. Original engine-generated Day 1 handoff report

The original `day01_handoff.zip` was recovered and its real engine-generated report is committed as [`reports/benchmark.json`](reports/benchmark.json).

### CSV — original handoff run

Recorded seconds:

`0.153875709, 0.211088204, 0.192891344, 0.199862060`

- Minimum: 0.153875709 s
- Median: **0.196376702 s**
- Maximum: 0.211088204 s

### Delta version 0 — original handoff run

Recorded seconds:

`1.466017986, 1.508811324, 2.565473854, 1.952296346`

- Minimum: 1.466017986 s
- Median: **1.730553835 s**
- Maximum: 2.565473854 s

The matching engine report records `query_results_match_oracle = true`, `equal_source_populations = true`, `positive_measured_samples = true` and `query_plans_saved = true`.

## 5. Later retained notebook execution

The consolidated executed Day 1 notebook contains a later valid rerun of the same small benchmark. That rerun observed:

### CSV — later notebook run

`0.204056404, 0.273864178, 0.200422811, 0.388999725`

Median: **0.238960291 s**

### Delta version 0 — later notebook run

`1.329042936, 1.373393722, 1.306951148, 1.760607276`

Median: **1.351218329 s**

Both runs returned the same 72-row / 1794.60 SAR business result. The timing difference between executions is itself a reason not to overclaim performance on this tiny local fixture.

## 6. Interpretation

In both retained executions, the CSV samples happened to be lower than the Delta samples. This observation **does not establish a general performance ranking**. Delta provides transaction, schema-enforcement, history and recovery capabilities exercised elsewhere in the project; a 72-row local micro-benchmark is too small to demonstrate production throughput or distributed scale.

## 7. Limits

These measurements cannot establish:

- real cloud cost;
- production scalability;
- guaranteed speed-up for either file format;
- distributed-cluster behaviour;
- steady-state performance under large data volumes.

Session startup, JVM state, filesystem cache and Delta metadata overhead can dominate a 72-row experiment. The cost values above are teaching assumptions and must not be presented as real currency or provider pricing.

## Evidence locations

- `reports/benchmark.json` — original engine-generated benchmark report recovered from the learner's Day 1 handoff.
- `reports/plans/csv.txt` — original CSV physical plan.
- `reports/plans/delta_v0.txt` — original Delta physical plan.
- `day01/STUDENT.ipynb` — later retained executed notebook evidence.
- `LAB02_NOTES.md` — summarized observation, decision and limitations.
