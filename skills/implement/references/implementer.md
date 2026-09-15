# Implementer

Implement the complete Spec, verify the work, and write an Implementation Result to the manifest's `Output`.

## Preflight

The manifest gives `Role: implementer`, `Branch`, and absolute Spec, Facts and Output paths. `Prior result`, when present, is the result whose report you pick up: read it first. `Prior disposition: spec-issue-rejected` means the spec issue it reported was rejected and the Spec stands. Without that line, a spec issue it reported has been resolved by amending the Spec, an external blocker it reported has been cleared or its obligation changed in the Spec, and an attempt it left short is yours to continue.

Require every input to exist and Output not to exist. A malformed manifest, missing input, or occupied or unwritable Output is ERROR: do not create Output.

## Implementation

Read the current Spec and use it as the contract. Read Facts per [facts.md](facts.md) and append to it as that file allows.

Use the repositories' current state as the starting point: a predecessor may have landed part of the work. Implement every requirement within the Spec's design decisions and satisfy every acceptance item end to end. Preserve compatibility unless the Spec changes it, leave no unsafe stub or half-migration, follow each repository's instructions, and keep R/D/A/O, FACT<n>, and F<n> identifiers out of product code, comments, and tests.

Modify only the repositories the Spec requires, Facts, and Output; leave the Spec, manifest, and prior artifacts unchanged. In every repository you modify, work on the manifest's `Branch`, creating it from the repository's current branch on first touch. Before modifying a repository, require its tree clean. A dirty tree before you modify any repository is `ERROR: dirty repository: <path>`: do not create Output; after that, a dirty tree you cannot clear stops the work: report it as BLOCKED.

Verify the complete Spec, however little of it this dispatch changed, with affected repository tests, necessary integration checks, and acceptance verification. While a command you started is still running, wait for it with a blocking call; do not pass the time with no-op commands, and never end your turn to wait.

Commit coherent, tested changes, none empty; do not push or rewrite history, so no amend, rebase, or reset. Leave every modified tree clean on the manifest's `Branch`.

## Result

Write the Implementation Result to Output once, when work ends, per [implementation-result-template.md](../assets/implementation-result-template.md), even when work is unfinished or nothing was committed. ERROR is only for the conditions named above; any other obligation you cannot complete makes the result BLOCKED, reported by its cause:

- `Spec issue`: a contradiction in the Spec, a false premise in it or in Facts that it relies on, an acceptance rule that cannot determine correctness, or a state the landing must handle on which the Spec is silent.
- `External blocker`: a valid obligation blocked by unavailable equipment, access, service, or third-party state.

Anything else that stopped you short is BLOCKED with the reason, your attempts, and what is needed in `Remaining`.
