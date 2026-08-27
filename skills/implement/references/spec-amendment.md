# Spec amendment

A role reported a spec issue; the reporting result is the issue source. Product implementation stays paused until this procedure ends.

## Verify

Check the issue source's claim against the current spec and the repositories' actual state.

If the claim does not hold, the spec stands: return to the reporting role's dispatch step and dispatch a successor with [the successor fields](../SKILL.md#successor-fields) — `Prior result` names the issue result, plus `Prior disposition: spec-issue-rejected`. Never route the old result's other categories. This ends the procedure.

## Decide

If the claim holds, choose the resolution:

- When the verified facts admit exactly one resolution that preserves the spec's intended outcomes — a false premise corrected, a contradiction whose right side the facts determine — decide it yourself.
- Otherwise tell the user what happened: the problem, its root cause, and the candidate resolutions with their consequences. They pick one, or uphold the current spec — then take the successor path in Verify.

## Draft and self-review

Write the complete replacement spec to a system temporary directory — do not touch the active spec yet. Preserve unchanged R/A identifiers, give new obligations unused identifiers, and never reuse removed ones.

Self-review the draft as the future authority: the decided resolution is fully applied, no new contradiction or false premise appears, and the identifier rules hold. Fix and repeat until clean.

Compute `Recheck`: the R/A already covered by completed phases whose coverage this amendment invalidates; new or never-completed R/A do not belong in it.

## Adopt

Pick one fresh `<name>`; write the draft to `$IMPLEMENT_DIR/decisions/<name>-spec.md` and this decision to `$IMPLEMENT_DIR/decisions/<name>.md`:

```text
# Spec Amendment
Issue source: <absolute-result-path>
Decided by: controller|user
Spec: <absolute-spec-path>
Proposal: <absolute-proposal-path>
Recheck:
- <R/A IDs or exact names, or (none)>
```

Append this record to `$PROGRESS_FILE`:

```text
Spec amendment — decisions/<name>.md
```

Only after adoption, atomically replace the active spec with the proposal; if the spec is tracked in Git, commit only the spec path on the repository's current branch.

## Continue

Continue to Step 5 and write a replacement scope for the still-active phase. `Recheck` invalidates only coverage established before this decision — subtract it from still-valid coverage when computing open work.
