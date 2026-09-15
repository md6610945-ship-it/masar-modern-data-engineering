# LAB02_NOTES.md

## Lab 02 — Cost and local scan evidence

### What I did
- Compared always-on compute with scheduled compute using hypothetical teaching units (TU), not currency.
- Used the provided cost model and its default assumptions.
- Verified the base case, break-even point, a counterexample, zero-rate behavior, and rejection of negative inputs.
- Ran the Spark benchmark on the same 72-trip dataset and retained all timing samples for CSV and Delta reads.
- Confirmed that both scan paths produced the same aggregate result.

### Cost assumptions used
- 30 days
- 24 hours/day for always-on compute
- 4 cores
- 0.50 TU per core-hour
- 100 GiB storage
- 0.20 TU per GiB-month
- 2 work hours/day for scheduled compute
- 0.25 startup hours/day
- 30 TU scheduled extra monthly overhead
- 0 TU always-on extra monthly overhead

### Observed cost-model results
- Always-on hours: 720
- Scheduled hours: 67.50
- Always-on compute: 1440.00 TU
- Scheduled compute: 135.0000 TU
- Storage for each policy: 20.00 TU
- Always-on total: 1460.00 TU
- Scheduled total: 185.0000 TU
- Difference: 1275.0000 TU
- Reduction fraction in this teaching scenario: 0.8732876712328767
- Break-even work hours/day: 23.25

### Cost checks
All checks passed:
- Base totals matched the expected values.
- At 23.25 work hours/day the difference is zero.
- At 23.75 work hours/day scheduled compute is no longer cheaper.
- With zero compute rate, break-even is undefined as expected.
- Negative input was rejected.

### Spark benchmark evidence
The benchmark returned the same aggregate result for the tested paths:
- Rows: 72
- Non-null fares: 72
- Fare total: 1794.60

CSV timing samples (seconds):
- 0.204056404
- 0.273864178
- 0.200422811
- 0.388999725
- Median: 0.238960291 s

Delta v0 timing samples (seconds):
- 1.329042936
- 1.373393722
- 1.306951148
- 1.760607276
- Median: 1.351218329 s

### What I observed
- Scheduled compute is much cheaper than always-on compute under the specific teaching assumptions used here.
- This is not universally true: the counterexample above the 23.25-hour break-even point shows that scheduled compute can cost more.
- On this very small dataset, the observed Delta scan was slower than the CSV scan in these samples.
- The repeated reads may be affected by OS, JVM, and metadata caching.

### Decision / interpretation
I will report the measured timings exactly as observed and will not claim a general performance speed-up from Delta based on this tiny dataset. The TU values are hypothetical teaching units and must not be presented as SAR or as a real cloud quotation.

### Evidence retained
- `reports/benchmark.json`
- Spark query plans for CSV and Delta
- `cost_model_result.json`
- Executed notebook outputs

### Limitations
- The dataset contains only 72 trips, so these timings are not production performance benchmarks.
- The timing test is not a controlled cold-cache benchmark.
- The cost model excludes many real cloud-pricing factors and is intended only to demonstrate compute/storage trade-offs.
