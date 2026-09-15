# LAB06 Notes — Quality Gate and Governance

## What I did

I ran the supplied Great Expectations quality suite at the promotion boundary, tested a mixed candidate containing the seven fixed quality cases, quarantined invalid records with explicit reasons and verified that a failed candidate could not replace the approved trusted snapshot.

## What I observed

The executed environment used Great Expectations 1.7.0. All saved quality checks passed:
- `trusted_and_rechecked_pass_gx = true`
- `mixed_candidate_fails_gx = true`
- `native_mixed_counts = true`
- `native_reasons_match_reference = true`
- `failed_candidate_not_promoted = true`
- `quarantine_delta_readback = true`
- `approved_readback_same_business_contents = true`
- `source_silver_untouched = true`
- `data_docs_exist_for_all_three_cases = true`

The mixed candidate contained 82 rows: 75 valid trusted trips plus 7 intentionally invalid quality cases. The seven rejected rows were quarantined with these reasons:
- missing trip ID → `MISSING_TRIP_ID`
- `SYN_BAD002` → `INVALID_FARE`
- `SYN_BAD003` → `UNKNOWN_DRIVER`
- `SYN_BAD004` → `INVALID_TIMESTAMP`
- `SYN_BAD005` → `INVALID_DURATION`
- `SYN_BAD006` → `INVALID_DISTANCE`
- `SYN_BAD007` → `INVALID_CITY`

The approved output remained 75 rows. The failed 82-row candidate was not promoted, and the source Silver table was not modified by the gate.

## Decisions

Quality validation occurs before promotion. Invalid rows are preserved in quarantine with a reason instead of being silently deleted, while only a newly validated snapshot is eligible for downstream use.

The governance record separates controls actually demonstrated in this lab from production controls that were not implemented. The project uses only the supplied synthetic data.

## Blockers / issues

The failure of the mixed candidate was intentional and is positive safety evidence, not an unresolved blocker. No unexpected blocking error remained.

## Evidence

Primary evidence is retained in the executed Day 4 notebook outputs, generated GX/Data Docs evidence, quarantine Delta output and the project `GOVERNANCE.md`.