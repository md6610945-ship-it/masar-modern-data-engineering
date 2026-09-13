# LAB02 Notes — Cost and Local Scan Evidence

## Objective
Use explicit assumptions to compare compute operating models, then collect actual Spark scan evidence with equal-result checks and saved query plans.

## Cost model
All cost values in this lab are hypothetical teaching units (TU), not SAR, USD, or real cloud-provider prices.

### Observed teaching-model result
- Always-on total: **1460.00 TU**
- Scheduled total: **185.0000 TU**
- Difference: **1275.00 TU**
- Break-even workload: **23.25 hours/day**

### Always-on compute
Keeps compute available continuously. It avoids startup waiting but charges for idle compute time.

### Scheduled compute
Starts compute for the workload and stops it afterwards. It can reduce idle compute for intermittent batch work, but startup and operating overhead still matter.

### Counterexample
Scheduled operation is not always cheaper. The notebook tested **23.75 work hours/day**, where the scheduled model became **30 TU more expensive** than always-on. This is why the operating policy must be interpreted from the workload rather than assumed in advance.

## Actual Spark scan evidence
The notebook compared equal-result scans of the same 72-trip population using CSV and Delta version 0. One warm-up was used per path and four measurements were retained for each.

Equal-result aggregate:
- Rows: **72**
- Non-null fares: **72**
- Fare total: **1794.60 SAR**

CSV samples (seconds):
- 0.10890634799943655
- 0.11094238599980599
- 0.11681015100020886
- 0.1130359579992728
- Median: **0.1119891719995394 s**

Delta v0 samples (seconds):
- 1.0241692199997487
- 0.7923787099998663
- 0.7902295410003717
- 0.9060651780000626
- Median: **0.8492219439999644 s**

The query plans were saved as:
- `reports/plans/csv.txt`
- `reports/plans/delta_v0.txt`

## Interpretation
CSV happened to be faster in this tiny run, but I do not treat that as a general speed-up claim. The dataset has only 72 base trips and the timings can be affected by Spark/JVM warm-up, OS and metadata caches, host load, and Delta overhead.

## Result
Lab 02 demonstrates compute-storage reasoning using explicit assumptions and actual measured scan evidence while keeping the conclusion bounded by the limitations of the small synthetic dataset.
