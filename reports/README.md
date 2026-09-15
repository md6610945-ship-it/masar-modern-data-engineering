# Reports and retained evidence

This directory separates **original engine-generated evidence** from notebook-derived summary evidence.

## Original runtime-generated evidence restored

The learner's real `day01_handoff.zip` was recovered and inspected. The following files were restored directly from that archive without reconstruction:

- `bronze.json` — original Day 1 Bronze engine report.
- `benchmark.json` — original Day 1 Spark benchmark report.
- `plans/csv.txt` — original CSV physical plan.
- `plans/delta_v0.txt` — original Delta physical plan.

The original benchmark run and the later retained notebook rerun have different elapsed-time samples, as expected for repeated local micro-benchmarks. `BENCHMARKS.md` reports both transparently and makes no general performance claim.

## Later-day evidence boundary

The available project artifacts include the executed Day 2–Day 5 notebooks with retained outputs, but the original `day04_handoff.zip` / `day05_handoff.zip` archives were not available when this repository was finalized. Therefore this repository does **not** fabricate `reports/quality/` or `reports/serving/` as if they were original engine-generated files.

The five committed `day01/`–`day05/STUDENT.ipynb` notebooks retain the observed execution outputs, and `notebook_evidence_summary.json` provides a machine-readable index transcribed from those retained outputs.

Generated Delta tables, checkpoints and bulky `outputs/` archives remain excluded from ordinary Git commits by `.gitignore`, consistent with the course submission rules.

## Evidence currently available

- `day01/STUDENT.ipynb`: source inspection, Bronze and later benchmark outputs.
- `day02/STUDENT.ipynb`: staging, Silver, late/replay and dbt outputs.
- `day03/STUDENT.ipynb`: transaction, negative-write and recovery-copy outputs.
- `day04/STUDENT.ipynb`: Kafka streaming and Great Expectations quality outputs.
- `day05/STUDENT.ipynb`: release recovery, BI reconciliation and AI availability outputs.
- `LAB01_NOTES.md`–`LAB08_NOTES.md`: human-readable observations and decisions.
- `BENCHMARKS.md`, `GOVERNANCE.md`, `DECISIONS.md`: project-level technical documentation.

This distinction between original generated evidence and retained notebook evidence is intentional and prevents overclaiming.