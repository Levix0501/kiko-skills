# Spec amendment

A role reported a spec issue; the reporting result is the issue source.

## Verify

Check the issue source's claim against the current spec and the repositories' actual state.

If the claim does not hold, the spec stands: dispatch a successor through the reporting result's [successor dispatch step](../SKILL.md#successor-dispatch-step), its manifest carrying `Prior disposition: spec-issue-rejected` below [`Prior result`](../SKILL.md#prior-result). Never route the old result's other categories. This ends the procedure.

## Decide

If the claim holds, choose the resolution:

- When the verified facts admit exactly one resolution that preserves the spec's intended outcomes and overrides no decision the user made — a false premise corrected, a contradiction whose right side the facts determine — decide it yourself.
- Otherwise tell the user what happened: the problem, its root cause, and the candidate resolutions with their consequences. They pick one, or uphold the current spec — then take the successor path in Verify.

## Draft and self-review

Draft the complete replacement spec without touching the active spec. Keep the identifier of every item that stays, give a new item an unused one, and never reuse a removed one.

Self-review the draft as the future authority: the decided resolution is fully applied, no new contradiction or false premise appears, every R and D has an A that decides it, and the identifier rules hold. Fix and repeat until clean.

## Adopt

Append the decision to `$DECISIONS_FILE` per [2.5 Decisions](../SKILL.md#25-decisions): the question states the issue and its root cause and names the issue source; the answer states the resolution and what changes in the spec. Mark it `[controller]` when you decided it and `[user]` when the user did, and append the facts it rests on to `$FACTS_FILE`. Append this record to `$PROGRESS_FILE`:

```text
Spec amendment — DECISION<n>
```

Then replace the active spec with the draft; if the spec directory is tracked in Git, commit the spec, `decisions.md`, and `facts.md` together on the repository's current branch.

## Continue

Dispatch a successor through the reporting result's [successor dispatch step](../SKILL.md#successor-dispatch-step): it brings the landing to the amended spec from the repositories' current state.
