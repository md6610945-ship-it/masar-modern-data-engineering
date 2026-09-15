# Pre-submission checklist

Status based on the repository, retained executed notebooks, recovered Day 1 handoff evidence and the SDA-DSC-214 submission requirements.

## Project evidence

- [x] Eight lab note files: `LAB01_NOTES.md` … `LAB08_NOTES.md`.
- [x] Five daily `day01/` … `day05/STUDENT.ipynb` files contain retained executed learner evidence and outputs.
- [x] `BENCHMARKS.md` contains assumptions, equal-result measurements, observed timings and limitations.
- [x] `GOVERNANCE.md` documents synthetic-data scope, lineage, roles, intended access and retention boundaries.
- [x] `DECISIONS.md` records architecture/performance/governance choices, alternatives and consequences.
- [x] `README.md` explains the project, architecture, environment, clean-clone setup, run order, actual results and limitations.
- [x] README states that the data is synthetic and includes SDAIA Academy / SDA-DSC-214 credit and `#SDAIAAcademy`.
- [x] Deliberate negative/failure evidence is retained in the executed notebooks and lab notes.
- [x] Trip/fare reconciliation is documented: 75 trips and 1880.60 SAR on both trusted/BI sides, difference 0 / 0.00 SAR.
- [x] `.gitignore` is present and excludes generated lakehouse/output archives, virtual environments, credentials and common caches.
- [x] Work is on the `develop` branch.

## Original machine-generated evidence recovered

- [x] `reports/bronze.json` restored directly from the learner's real Day 1 handoff archive.
- [x] `reports/benchmark.json` restored directly from the same handoff archive.
- [x] `reports/plans/csv.txt` restored from the original run.
- [x] `reports/plans/delta_v0.txt` restored from the original run.
- [x] Additional original Day 1 source-inspection and cost-model reports retained under `reports/`.

## Evidence boundary still open

- [ ] **Original `reports/quality/` and `reports/serving/`:** executed Day 4/Day 5 notebook outputs are present, but the original later-day handoff archives are not available in the retained file set. Do not fabricate these files. If a real Day 4 or Day 5 handoff archive becomes available, restore its small generated reports before submission.

## Repository metadata / submission

- [x] GitHub About description describes the learner project.
- [ ] Add relevant GitHub Topics (for example: `data-engineering`, `pyspark`, `delta-lake`, `kafka`, `lakehouse`, `great-expectations`, `dbt`).
- [x] Public-access check completed by opening the `develop` branch in a private/incognito browser window while signed out.
- [ ] After all final repository changes, copy the final `develop` commit SHA.
- [ ] Submit the `develop` branch URL + final commit SHA + `#SDAIAAcademy` through the organizer's announced channel.

## Important note

Do not invent missing machine-generated evidence, do not backdate Git history and do not convert an earlier/later repeated benchmark into a false single execution. The repository distinguishes original engine-generated files from retained notebook evidence intentionally.