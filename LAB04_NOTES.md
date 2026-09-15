# LAB04 Notes — Delta Operations and Safe Change

## What I did

I used the trusted Silver trip table to exercise Delta transaction history, schema enforcement, correction handling, earlier-version reads and maintenance/recovery operations. Destructive maintenance exercises were run only on isolated training copies so the trusted Silver table remained unchanged.

## What I observed

### Transaction and correction checks

All transaction checks passed:
- `native_correction_matches_source_expectation = true`
- `business_rows_stay_75 = true`
- `replay_and_stale_delivery_preserve_values = true`
- `same_revision_conflict_rejected = true`
- `actual_prior_version_read = true`
- `mixed_valid_invalid_batch_rejected_atomically = true`

The trusted table had 75 business rows before and after the correction. The observed table version moved from version 1 before the correction to version 2 after it, while the logical row count stayed 75. I also successfully read the prior version (version 1).

The correction changed the business-content digest, as expected, while replaying the correction or presenting stale data did not create extra business rows. A conflicting same-revision record and a mixed valid/invalid batch were rejected rather than partially published.

### Maintenance and recovery-copy checks

All maintenance checks passed:
- `unexpected_column_rejected = true`
- `approved_evolution_preserves_business_values = true`
- `compaction_preserves_values = true`
- `delete_affects_copy_only = true`
- `restore_creates_new_commit = true`
- `vacuum_is_non_destructive_dry_run = true`
- `trusted_silver_unchanged = true`

On the isolated recovery copy, the row count changed from 75 at version 0 to 74 after the training delete, then returned to 75 after restore at version 2. The restored business-content digest matched the pre-delete digest.

## Decisions

I relied on Delta schema enforcement and transaction history rather than silently coercing or dropping invalid writes. Corrections are explicit changes, while stale deliveries and retries must not overwrite newer business state.

Potentially destructive exercises such as delete, restore and maintenance were isolated from the trusted Silver table. The production-style principle demonstrated here is that recoverability must be proven without risking the authoritative table.

## Blockers / issues

No blocking error remained. Several negative tests intentionally failed, which was the expected evidence: invalid schema, same-revision conflict and mixed invalid writes were rejected safely.

## Evidence

Primary evidence is retained in the executed Day 3 notebook outputs, including Delta history, prior-version reads, rejection evidence and the isolated restore exercise.