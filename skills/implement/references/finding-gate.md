# Finding gate

Read this reference when Step 9 routes a `Fix: DONE` result to the user: three rounds of review against an unchanged spec have each found and fixed issues, and the last round's fixes are unverified.

Show the user the findings of every round from the latest result, with severity, and what the last round changed, so they can judge why the reviews are not converging. Ask how to proceed:

- `Fix again` → Step 8: a fresh reviewer verifies the last fixes and fixes what it finds; this gate is read again after its `Fix: DONE`.
- `Accept risk` → record the acceptance below, then continue to Step 13.
- `Incomplete` → Step 14.

If the user sees a defect in the Spec behind the churn, follow [spec-amendment.md](spec-amendment.md) with their claim in place of a result's, the latest result being the reporting result; should the spec stand, return to the choice above rather than dispatching.

For accepted risk, append a `[user]` decision to `$DECISIONS_FILE` per [2.5 Decisions](../SKILL.md#25-decisions): the question names the last round's findings by `F<n>` and text and states that their fixes are unverified; the answer records the user's acceptance and their reason in their words if given. Append this record to `$PROGRESS_FILE`:

```text
Risk acceptance — DECISION<n>
```
