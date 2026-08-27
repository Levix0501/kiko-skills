# Finding gate and risk acceptance

Read this reference when a re-review leaves `issues` with a Critical or Important finding, or on a final phase; the choice is the user's.

A finding is `F<n> | critical|important|minor | <self-contained finding>`. `contract-blocking:` is a finding-text prefix, not a severity. It means a current Requirement, Acceptance, Landing, or necessary Verify cannot be established; it must be Important or Critical and cannot be risk-accepted.

The open wave is every finding the re-review defines or marks `not_addressed`. Show it complete — source, severity, full finding, contract-blocking state — and offer only `Fix again`, `Accept risk`, and `Incomplete`:

- `Fix again` opens the next round at Step 11.
- Offer `Accept risk` only when the wave has no contract-blocking finding. It covers the complete wave unless the user names a split between accepted findings and findings to fix.
- `Incomplete` ends the run at Step 18; it creates no decision artifact or progress record.

If the user instead determines that a finding exposes a defective spec, use that result and exact finding reference as the issue source and follow [spec-amendment.md](spec-amendment.md). This reactive route is not a fourth default option.

For accepted risk, pick one fresh `<name>` and write `$IMPLEMENT_DIR/decisions/<name>.md`:

```text
# Risk Acceptance
Source result: <absolute-re-review-result-path>
Accepted findings:
- <absolute-defining-result-path>#F<n>
Risk: <specific accepted consequence>
Reason: <user reason; omit when none was given>
```

Append this record to `$PROGRESS_FILE`:

```text
Phase P<n>: risk acceptance — decisions/<name>.md
```

Exact finding references close only those findings; a finding newly reported at later Heads needs a new decision. Do not copy Heads into the decision.

Then route what remains: findings the acceptance leaves open go to the next round at Step 11; if it closed the whole wave, the round's obligations are met — follow Step 13's `clean` route.
