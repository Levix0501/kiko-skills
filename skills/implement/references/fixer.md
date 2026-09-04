# Finding-Wave Fixer

Resolve one complete manifest-defined finding wave, verify and commit the result, and write structured evidence to the manifest's `Output`.

## Preflight

Require `Role: fixer`, `Action: execute|evidence-recovery`, `Mode: intermediate|final`, `Level: code|model`, `Branch`, absolute Spec, Scope, Notes and Output paths, Evidence sources, and Finding sources.

Each Finding source is an absolute `<defining-result-path>#F<n>` reference and must resolve to one self-contained finding. `Prior result` is valid for a successor after a code blocker, an external blocker, or a rejected spec issue. The rejected-spec case also requires `Prior disposition: spec-issue-rejected`; that disposition is invalid without `Prior result`. `Recovery evidence` is required exactly when `Action` is `evidence-recovery` and names the blocked result whose verification is being recovered.

Before work, require every input to exist and Output not to exist. A malformed manifest, missing source, or occupied/unwritable Output is ERROR: do not create Output.

Use Spec, Scope, Evidence sources, and every Finding source as the contract. Resolve the whole wave, including its Minor findings. Decide which product repositories the fixes require and modify only those repositories and the exact Output; do not modify the Spec, Scope, manifest, or prior artifacts. In every repository you modify, work on the manifest's `Branch`, creating it from the repository's current branch on first touch. Before modifying a repository, require its tree clean. A dirty tree before you modify any repository is `ERROR: dirty repository: <path>`: do not create Output; after that, a dirty tree you cannot clear is a `Blocker`. Read Notes per [notes.md](notes.md); append to it only as that file allows.

## Execute

For `Action: execute`, treat the repositories' current state as the starting point — a predecessor may have landed part of the work — and resolve the complete wave:

- fix each supplied root problem without expanding the current contract; at `Level: model`, first append the revised or new invariant to Notes, then make the code hold it;
- preserve the intermediate stable landing, or in final mode preserve the whole integrated product contract;
- follow repository instructions and, before DONE, run affected tests, integration checks, Scope Verify, and acceptance verification, exercising the break conditions of every invariant the wave names;
- commit coherent changes without rewriting existing history; do not create empty commits for unchanged repositories; and
- leave every modified tree clean on the manifest's `Branch`. Do not push, amend, rebase, or reset.

If a finding appears invalid, do not close it. Finish all other actionable work and add a `Concern` naming the finding and evidence; the re-reviewer owns the verdict.

For `Action: evidence-recovery`, make no product or Git changes. Require every repository to match the `Recovery evidence` repository blocks — same Branch and Head, clean tree; a deviation is `ERROR: dirty repository: <path>` or `ERROR: moved repository: <path>`. Re-run or recollect the required verification at those Heads and write successor evidence reporting the same repositories at the same Heads.

A contradiction in the current Spec, a false premise in it or in Notes that it relies on, an acceptance rule that cannot determine correctness, or a state the landing must handle on which the Spec is silent, is a `Spec issue`. A valid obligation blocked by unavailable equipment, access, service, or third-party state is an `External blocker`. Any other impediment you cannot clear is a `Blocker`. Incomplete wave resolution yields BLOCKED, not ERROR; commit only coherent tested landings and report every actual Head.

## Evidence file

Write Output once using this complete schema:

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
Concern: <finding ID and non-blocking issue for re-review; repeat or omit>
Blocker: <code blocker> | next: <specific action needed>; repeat or omit
Spec issue: <problem and root cause>; repeat or omit
External blocker: <affected R/A> | owner: <owner> | action: <specific user action> | verify: <procedure>; repeat or omit
Remaining: <unfinished finding references or obligations; required only for BLOCKED>
Evidence-complete: yes
```

Repeat the repository block for every repository you modified. `Branch` is the manifest's `Branch`. `Base` is the commit the work started from — on first touch, the commit the `Branch` was created from. `Head` is the completed Head. Keep command results concise and omit full logs.

DONE means the complete finding wave is resolved as far as the fixer can act, verified, committed, and clean; an allegedly invalid finding is represented by Concern for re-review. DONE contains no Blocker, Spec issue, External blocker, or Remaining. BLOCKED requires Remaining and at least one applicable issue category. Create complete evidence even when execution began but no product commit was possible.
