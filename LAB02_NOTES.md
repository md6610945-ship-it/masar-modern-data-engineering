# LAB02 Notes — Cost and Local Scan Evidence

## What I did

I evaluated the teaching cost model for always-on versus scheduled compute, stated the assumptions explicitly, calculated the break-even point and checked a counterexample. I then measured equivalent Spark scans over the fixed trip data and compared equal-result CSV and Delta reads while saving the query plans.

## Cost assumptions

All cost values below are **hypothetical teaching units (TU), not SAR or another currency**.

- 30 days per month
- 24 hours per day
- 4 cores
- 0.50 TU per core-hour
- 100 GiB storage
- 0.20 TU per GiB-month
- Scheduled workload: 2 work hours/day plus 0.25 startup hours/day
- Scheduled fixed monthly overhead: 30 TU
- Always-on fixed monthly overhead: 0 TU

## What I observed

For the base scenario:
- Always-on runtime: 720 hours/month
- Scheduled runtime: 67.50 hours/month
- Always-on compute: 1440 TU
- Scheduled compute: 135 TU
- Storage: 20 TU in either design
- Always-on total: 1460 TU
- Scheduled total: 185 TU
- Difference: 1275 TU
- Teaching-model reduction: about 87.33%
- Break-even work time: 23.25 hours/day

The counterexample also worked as intended: at 23.75 work hours/day, the scheduled design costs 1490 TU, which is 30 TU more than the always-on design. Therefore scheduled compute is not automatically cheaper under every assumption.

### Spark scan evidence

The equal-result check returned:
- 72 rows
- 72 non-null fares
- total fare = 1794.60 SAR

Observed CSV scan samples (seconds):
- 0.204056404
- 0.273864178
- 0.200422811
- 0.388999725
- median: 0.238960291

Observed Delta version-0 scan samples (seconds):
- 1.329042936
- 1.373393722
- 1.306951148
- 1.760607276
- median: 1.351218329

The CSV and Delta scans returned the same business result. Query plans were saved during the run for both paths.

## Decisions

I separated hypothetical cost assumptions from measured elapsed times. I also used an equal-result test before comparing scan timings so the benchmark did not compare different work.

## Limitations

This is a tiny synthetic dataset in a local/Colab teaching environment. The timing samples do not prove that CSV is generally faster than Delta, do not establish production scalability and do not estimate real cloud spend. JVM/OS caching, metadata work and session state can materially affect such small measurements.

## Blockers / issues

No blocking error was observed. All cost-model checks and benchmark result-equality checks completed successfully.

## Evidence

Primary evidence is retained in the executed Day 1 notebook outputs. The runtime also generated benchmark evidence and query plans under the lab `reports/` workspace.