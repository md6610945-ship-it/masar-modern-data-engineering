# Reports and retained evidence

The original lab runs created machine-generated JSON reports inside the Colab workspace/handoff archives (for example `reports/bronze.json`, benchmark plans, Day 4 quality reports and Day 5 serving reports). The uploaded learner artifact available for this repository cleanup was the **executed consolidated notebook**, not the original `outputs/dayNN_handoff.zip` workspace.

To avoid fabricating machine-generated artifacts, this repository does **not** pretend that reconstructed JSON files are the original engine reports. The five committed `day01/`–`day05/STUDENT.ipynb` notebooks retain the observed execution outputs, and `notebook_evidence_summary.json` provides a machine-readable index transcribed from those retained outputs.

If the original Day 5 handoff ZIP is available, its real generated `reports/` content should be restored as the final evidence source. Generated Delta tables, checkpoints and bulky `outputs/` archives remain excluded from normal Git commits by `.gitignore`.

## Evidence currently available

- `day01/STUDENT.ipynb`: source inspection, Bronze and benchmark outputs.
- `day02/STUDENT.ipynb`: staging, Silver, late/replay and dbt outputs.
- `day03/STUDENT.ipynb`: transaction, negative-write and recovery-copy outputs.
- `day04/STUDENT.ipynb`: Kafka streaming and Great Expectations quality outputs.
- `day05/STUDENT.ipynb`: release recovery, BI reconciliation and AI availability outputs.
- `LAB01_NOTES.md`–`LAB08_NOTES.md`: human-readable observations and decisions.
- `BENCHMARKS.md`, `GOVERNANCE.md`, `DECISIONS.md`: project-level technical documentation.

The distinction between original generated evidence and transcribed notebook evidence is intentional and prevents overclaiming.