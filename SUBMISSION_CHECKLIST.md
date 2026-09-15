# Pre-submission checklist

Status based on the repository cleanup performed from the learner's executed consolidated notebook and the SDA-DSC-214 submission requirements.

## Project evidence

- [x] Eight lab note files: `LAB01_NOTES.md` … `LAB08_NOTES.md`.
- [x] Five daily `day01/` … `day05/STUDENT.ipynb` files contain retained executed learner evidence and outputs extracted from the consolidated run.
- [x] `BENCHMARKS.md` contains assumptions, equal-result measurements, observed timings and limitations.
- [x] `GOVERNANCE.md` documents synthetic-data scope, lineage, roles, intended access and retention boundaries.
- [x] `DECISIONS.md` records architecture/performance/governance choices, alternatives and consequences.
- [x] `README.md` explains the project, architecture, environment, run order, actual results and limitations.
- [x] README states that the data is synthetic and includes SDAIA Academy / SDA-DSC-214 credit and `#SDAIAAcademy`.
- [x] Deliberate negative/failure evidence is retained in the executed notebooks and lab notes.
- [x] Trip/fare reconciliation is documented: 75 trips and 1880.60 SAR on both trusted/BI sides, difference 0 / 0.00 SAR.
- [x] `.gitignore` is present and excludes generated lakehouse/output archives, virtual environments, credentials and common caches.
- [x] Work is on the `develop` branch.

## Evidence boundary to verify before final submission

- [ ] **Original runtime-generated `reports/` files:** the supplied artifact for repository cleanup was the executed consolidated notebook, not the original `day05_handoff.zip`. The repository therefore contains a transparent notebook-derived evidence index under `reports/`, not a fabricated copy of the original engine-generated reports. If the original handoff ZIP still exists, restore its small generated reports before submission.
- [ ] **GitHub About/topics:** set the repository About text to describe this learner project and mark the data as synthetic; add relevant topics if desired. This is GitHub repository metadata, not a file in the repository.
- [ ] Open the final `develop` branch URL in a private/incognito window to confirm public access.
- [ ] Copy the final commit SHA and include it with the branch URL and `#SDAIAAcademy` in the organizer's submission channel.

## Important note

Do not invent missing machine-generated evidence and do not backdate Git history. The committed documentation is intentionally limited to what the executed notebook supports.