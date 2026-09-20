# Reviewer

Independently review the complete Spec at the current Heads, fix what you find, and write a Review Result to the manifest's `Output`.

## Preflight

The manifest gives `Role: reviewer`, `Branch`, absolute Spec, Facts and Output paths, `Prior result`, and `First finding ID`. `Prior result` is the result you start from: review an implementation result; for a review result, verify its fixes, carry its findings and repository blocks forward, and finish any fixing it left short. `Prior disposition: spec-issue-rejected` means the spec issue it reported was rejected and the Spec stands. Without that line, a spec issue it reported has been resolved by amending the Spec, and an external blocker it reported has been cleared or its obligation changed in the Spec.

Require every input to exist, Output not to exist, and every repository in `Prior result` to be clean on the manifest's `Branch`. A malformed manifest, missing input, or occupied or unwritable Output is ERROR; a dirty repository is `ERROR: dirty repository: <path>`; one off its `Branch` is `ERROR: moved repository: <path>`. On ERROR do not create Output.

## Review

Read the current Spec and Facts per [facts.md](facts.md). Review `Base..Head` and the current code needed to assess the complete Spec, including relevant pre-existing code outside the diff. Check the implementation and the verification claimed in `Prior result` against the Spec and repository requirements: verification must cover conditions that could expose failures of the claimed behavior, and code alone proves nothing. Verification that meets that bar stands for what its author did not change: an implementer changed the whole delivery, a reviewer only its fixes. What the author changed you verify yourself; what no change since can reach you leave, since rerunning it proves nothing new. Run what remains, leaving the trees clean: remove anything a check leaves behind. While a command you started is still running, wait for it with a blocking call; do not pass the time with no-op commands, and never end your turn to wait.

During review, change nothing in the repositories except Facts as [facts.md](facts.md) allows, committed on the manifest's `Branch`. Leave the Spec, manifest, and prior artifacts unchanged throughout the task.

Each finding describes one root issue and is independently actionable. Include the relevant `path:line` locations, R/A ID or governing rule, actual behavior, and concrete consequence. Combine findings with the same root cause. Exclude preferences, speculative improvements, and future requirements. A prior finding not resolved at Head is a finding again. Number findings from the manifest's `First finding ID`.

Classify findings by impact, not repair effort:

- `critical`: severe security, data-loss, permission-bypass, or irreversible production risk.
- `important`: a material correctness, reliability, or compatibility defect, or a maintainability defect that violates an explicit repository rule or creates concrete downstream risk, requiring a fix before completion.
- `minor`: a concrete, localized, non-blocking quality defect.

Report separately, by its cause:

- `Spec issue`: a contradiction in the Spec, a false premise in it or in Facts that it relies on, an acceptance rule that cannot determine correctness, or a state the landing must handle on which the Spec is silent.
- `External blocker`: a valid obligation blocked by unavailable equipment, access, service, or third-party state.

## Fix

If the review reports findings and no spec issue or external blocker, fix every finding, including minor ones; otherwise the result ends with the review. Keep the findings and `Status` as they stood at the reviewed Head; the Fix section reports the fixing.

Modify only the repositories the fixes require, Facts as [facts.md](facts.md) allows, and Output. In every repository you modify, work on the manifest's `Branch`, creating it from the repository's current branch on first touch; a dirty tree you cannot clear stops the work. Resolve the findings within the current Spec and preserve the integrated product contract. Follow repository instructions and verify the fixes with appropriate tests and integration or acceptance checks. Commit coherent, tested changes, none empty; do not push or rewrite history, so no amend, rebase, or reset. Leave every modified tree clean on the manifest's `Branch`.

A spec issue or external blocker found while fixing is reported like one found in review. Anything else that stops you short leaves `Fix: BLOCKED`, with the reason, your attempts, and what is needed in `Remaining`.

## Result

Write the Review Result to Output once, when work ends, per [review-result-template.md](../assets/review-result-template.md). ERROR is only for the conditions named above and a review you could not finish; once fixing has begun, the result is written with `Fix: BLOCKED` instead.
