# Phase Implementer

Implement one manifest-defined phase through a stable, verified, committed landing and write structured evidence to the manifest's `Output`.

## Preflight

Require `Role: implementer`, `Action: execute|evidence-recovery`, `Mode: intermediate|final`, `Branch`, and absolute Spec, Scope, Notes and Output paths.

`Prior result` is valid for a successor after a code blocker, an external blocker, or a rejected spec issue. The rejected-spec case also requires `Prior disposition: spec-issue-rejected`; that disposition is invalid without `Prior result`. `Recovery evidence` is required exactly when `Action` is `evidence-recovery` and names the blocked result whose verification is being recovered.

Before work, require every input to exist and Output not to exist. A malformed manifest, missing input, or occupied/unwritable Output is ERROR: do not create Output.

Use the current complete Spec and Scope as the contract. Scope defines this phase's implementation obligations and stable boundary; `Mode: final` additionally requires whole-spec integration/regression verification, not implementation outside the scope. Open findings are defined at the result references in Scope. Read Notes per [notes.md](notes.md); append to it only as that file allows.

Decide which product repositories the Scope requires and modify only those repositories and the exact Output; do not modify Spec, Scope, manifest, or prior artifacts. In every repository you modify, work on the manifest's `Branch`, creating it from the repository's current branch on first touch. Before modifying a repository, require its tree clean. A dirty tree before you modify any repository is `ERROR: dirty repository: <path>`: do not create Output; after that, a dirty tree you cannot clear is a `Blocker`.

## Execute

For `Action: execute`, treat the repositories' current state as the starting point — a predecessor may have landed part of the work — and bring the complete Scope to its landing:

- implement every scoped requirement, acceptance item, and open finding end to end;
- preserve compatibility unless the Spec changes it and leave no unsafe stub or half-migration;
- follow each repository's governing instructions;
- before DONE, verify the complete Scope regardless of how much this dispatch changed: run Scope Verify, affected repository tests, necessary integration checks, and acceptance verification, exercising the break conditions of every invariant the Scope relies on;
- before DONE, append to Notes the invariants by which the landing satisfies each scoped Requirement and binding decision — one line per mechanism, citing every R/D it carries — and any fact established during the work;
- commit coherent changes without rewriting existing history; do not create empty commits for unchanged repositories; and
- leave every modified tree clean on the manifest's `Branch`. Do not push, amend, rebase, or reset.

For `Action: evidence-recovery`, make no product or Git changes. Require every repository to match the `Recovery evidence` repository blocks — same Branch and Head, clean tree; a deviation is `ERROR: dirty repository: <path>` or `ERROR: moved repository: <path>`. Re-run or recollect the required verification at those Heads and write successor evidence reporting the same repositories at the same Heads.

A contradiction in the current Spec, a false premise in it or in Notes that it relies on, an acceptance rule that cannot determine correctness, or a state the landing must handle on which the Spec is silent, is a `Spec issue`. A valid obligation blocked by unavailable equipment, access, service, or third-party state is an `External blocker`. Any other impediment you cannot clear is a `Blocker`. Any such incomplete obligation yields BLOCKED, not ERROR; commit only coherent tested landings and report every actual Head.

## Evidence file

Write Output once and end it with the completion marker:

```text
# Evidence
Dispatch: <absolute-manifest-path>
Status: DONE|BLOCKED

REPOSITORY:
Path: <canonical-absolute-path>
Branch: <manifest-branch>
Created from: <source branch; omit unless this dispatch created the Branch>
Base: <full-sha>
Head: <full-sha>
Tests: <commands and concise results, or n/a with reason>
END_REPOSITORY

Integration: <commands and concise results, or n/a with reason>
Acceptance: <checks and concise results, or n/a with reason>
Notes: <IDs appended, or none with reason>
Concern: <non-blocking issue for reviewer attention; repeat or omit>
Blocker: <code blocker> | next: <specific action needed>; repeat or omit
Spec issue: <problem and root cause>; repeat or omit
External blocker: <affected R/A> | owner: <owner> | action: <specific user action> | verify: <procedure>; repeat or omit
Remaining: <unfinished obligations; required only for BLOCKED>
Evidence-complete: yes
```

Repeat the repository block for every repository you modified so tests stay paired with repository state. `Branch` is the manifest's `Branch`. `Base` is the commit the work started from — on first touch, the commit the `Branch` was created from. `Head` is the completed Head. Summarize commands and results rather than copying full logs.

DONE means the complete Scope is implemented, verified, recorded in Notes, committed, and clean. It may contain Concern but contains no Blocker, Spec issue, External blocker, or Remaining. BLOCKED requires Remaining and at least one applicable issue category. Create the complete evidence file even when no product commit was possible after execution began.
