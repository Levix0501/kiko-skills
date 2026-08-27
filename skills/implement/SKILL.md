---
name: implement
description: Turn a spec into reviewed, independently shippable code with adaptive phases. Use when the user asks to build a spec.
---

Orchestrate implementation from a spec: the controller dispatches fresh roles (subagents) to implement, review, fix, and re-review.

## 1 Workflow

Start with Step 1.

### Step 1: Check kiko workspace

Run the bundled `scripts/check-kiko` with `$PROJECT_ROOT` — the opened project root's absolute path, not the current shell directory — as its sole argument, and treat the returned path as `$KIKO_ROOT`.

- Exit 3 means the layout is missing or incomplete: ask the user to run `/setup-kiko` at the project root, then retry.
- For any other nonzero exit, report the error and stop.

Never initialize or repair `.kiko` yourself. On success, continue to Step 2.

### Step 2: Resolve spec

Resolve `SPEC_FILE`, the spec's absolute path, from the first available source:

1. the path supplied by the user;
2. the spec already selected in the current context;
3. ask which spec to build.

Then continue to Step 3.

### Step 3: Prepare the implementation directory

Run the bundled `scripts/prepare-implement-dir` with `$KIKO_ROOT` and `$SPEC_FILE` as its arguments, and treat the returned path as `$IMPLEMENT_DIR` — [2.1 Implementation directory](#21-implementation-directory). On a nonzero exit, show the error and ask the user to fix what it names, then retry from Step 2.

On success, derive:

```sh
SPEC_NAME=$(basename "$SPEC_FILE")
SPEC_SLUG=${SPEC_NAME%.md}
SPEC_POINTER=".kiko/specs/$SPEC_NAME"
```

Then check every repository under `$PROJECT_ROOT` for uncommitted content: if any tree is not clean, show the user what you found and offer the choice — continue after they clean it, or end the run (Step 18).

Then continue to Step 4.

### Step 4: Read progress file

```sh
PROGRESS_FILE="$IMPLEMENT_DIR/progress.md"
```

- `$PROGRESS_FILE` — [2.2 Progress file](#22-progress-file).

If `$PROGRESS_FILE` does not exist, create it empty. If it is empty, continue to Step 5. Otherwise resume: read the records and the artifacts they point to, determine which step the run reached, and continue from that step.

### Step 5: Plan the next phase

Read the current complete spec and the adopted artifact chain. Open work is every current requirement and acceptance item minus still-valid coverage from completed phases, plus every open finding.

Plan each phase as final by default. Split off an intermediate phase only when both hold: the largest coherent subset can land in a stable, compatible, independently safe and verifiable state, and the boundary either materially isolates high risk or keeps a fresh implementation and review reliable.

A phase is final exactly when its scope holds all remaining requirements, acceptance items, and open findings.

Number the phase by its entry: the initial phase is P1; after an intermediate phase completes, the next phase is `P<n+1>`; a replacement scope for the still-active phase keeps its `P<n>`.

Write the scope to `$IMPLEMENT_DIR/scopes/<name>.md`, in the fixed shape defined in [2.3 Scope](#23-scope). Append this record to `$PROGRESS_FILE`:

```text
Phase P<n>: scope — scopes/<name>.md
```

Then continue to Step 6.

### Step 6: Dispatch the implementer

Write `$IMPLEMENT_DIR/dispatch/<name>.md` — a manifest, per [2.4 Manifests](#24-manifests):

```text
Role: implementer
Action: execute
Mode: intermediate|final
Branch: kiko/$SPEC_SLUG
Spec: $SPEC_FILE
Scope: <current-scope-absolute-path>
Output: $IMPLEMENT_DIR/results/<name>.md
```

`Mode` is `final` for a final phase, otherwise `intermediate`. `Branch` is the spec's work branch: in every repository it modifies, the role works on this branch, creating it from the repository's current branch on first touch.

Append this record to `$PROGRESS_FILE`:

```text
Phase P<n>: implement — dispatch/<name>.md
```

Then dispatch a fresh subagent with the prompt in [2.5 Dispatch prompt](#25-dispatch-prompt); its role contract is `references/implementer.md` and its manifest is the file just written.

When the subagent returns, continue to Step 7.

### Step 7: Validate the subagent return

An `ERROR:` return is a protocol failure: return to the dispatching step and dispatch again. One exception: an ERROR reporting a dirty or moved repository is the user's decision — show it and offer the choice: retry after they restore the repository (return to the dispatching step), or end the run (Step 18).

Otherwise validate the Output mechanically. For every role: it sits at the manifest's `Output` path and is complete per the role's contract. For a write role (implementer, fixer): its reported repositories match reality — each on the manifest's `Branch` at its reported Head, Base an ancestor of Head, tree clean. For a read role (reviewer, re-reviewer): the reviewed repositories are unchanged — each still on its target branch and Head with a clean tree — its finding IDs run consecutively from the manifest's `First finding ID`, and on a final phase the coverage lists every current Requirement and Acceptance exactly once in spec order. A missing or invalid Output is a protocol failure as well: return to the dispatching step and dispatch again. A second consecutive protocol failure at the same dispatch step, and each one after it, is the user's decision — show what failed and offer the choice: retry (return to the dispatching step), or end the run (Step 18).

Adopt the result — append the line matching the dispatched role to `$PROGRESS_FILE`, reusing the dispatch's `<name>`:

```text
Phase P<n>: implementation result — results/<name>.md
Phase P<n>: review result — results/<name>.md
Phase P<n>: fix result — results/<name>.md
Phase P<n>: re-review result — results/<name>.md
```

Then continue by result type: an implementation result or a fix result → Step 8; a review result → Step 10; a re-review result → Step 13.

### Step 8: Route the write-role result

- `DONE` — for an implementation result, continue to Step 9; for a fix result, continue to Step 12. A Concern in DONE evidence is review input, not a blocker.
- `BLOCKED` — handle only the highest-priority reported category, in this order: spec issue (Step 14), then external blocker (Step 15), then code blocker (Step 16); never route the result's lower-priority conclusions directly.

### Step 9: Dispatch the reviewer

Write `$IMPLEMENT_DIR/dispatch/<name>.md` — a manifest, per [2.4 Manifests](#24-manifests):

```text
Role: reviewer
Mode: intermediate|final
Spec: $SPEC_FILE
Scope: <current-scope-absolute-path>
Output: $IMPLEMENT_DIR/results/<name>.md

Evidence sources:
- <absolute-evidence-path>
First finding ID: F<n>

REVIEW_TARGET:
Path: <canonical-absolute-path>
Branch: kiko/$SPEC_SLUG
Base: <full-sha>
Head: <full-sha>
END_REVIEW_TARGET
```

`Mode` is `final` for a final phase, otherwise `intermediate`. `Evidence sources` list the current phase's adopted evidence, DONE and BLOCKED alike. `First finding ID` is one greater than the highest finding ID in any adopted review or re-review result, or `F1`. Repeat `REVIEW_TARGET` per repository any adopted evidence reports: `Head` is that repository's Head in the latest adopted evidence reporting it; `Base` is its Head in the last adopted evidence from before the current phase — or, where this phase touches it first, its first reporting evidence's `Base`, the work branch's starting commit.

Append this record to `$PROGRESS_FILE`:

```text
Phase P<n>: review — dispatch/<name>.md
```

Then dispatch a fresh subagent with the prompt in [2.5 Dispatch prompt](#25-dispatch-prompt); its role contract is `references/reviewer.md` and its manifest is the file just written.

When the subagent returns, continue to Step 7.

### Step 10: Route the review result

- `clean` — the phase's obligations are proven: for an intermediate phase, continue to Step 5 to plan the next phase; for a final phase, the run is Complete: continue to Step 17.
- `issues` with any Critical or Important finding, or on a final phase — the findings open a fix round: continue to Step 11.
- `issues` with only Minor findings on an intermediate phase — fixing defers: the phase completes and the findings carry as open findings; continue to Step 5 to plan the next phase.
- A reported spec issue or external blocker outranks status routing — handle only the highest-priority reported category, in this order: spec issue (Step 14), then external blocker (Step 15); never route the result's lower-priority conclusions directly.

### Step 11: Dispatch the fixer

Write `$IMPLEMENT_DIR/dispatch/<name>.md` — a manifest, per [2.4 Manifests](#24-manifests):

```text
Role: fixer
Action: execute
Mode: intermediate|final
Branch: kiko/$SPEC_SLUG
Spec: $SPEC_FILE
Scope: <current-scope-absolute-path>
Output: $IMPLEMENT_DIR/results/<name>.md

Evidence sources:
- <absolute-evidence-path>
Finding sources:
- <defining-result-path>#F<n>
```

`Mode` is `final` for a final phase, otherwise `intermediate`. `Evidence sources` list the current phase's adopted evidence, DONE and BLOCKED alike. `Finding sources` are the round's wave: every finding the triggering result defines or marks `not_addressed`, minus findings covered by a risk acceptance.

Append this record to `$PROGRESS_FILE`:

```text
Phase P<n>: fix — dispatch/<name>.md
```

Then dispatch a fresh subagent with the prompt in [2.5 Dispatch prompt](#25-dispatch-prompt); its role contract is `references/fixer.md` and its manifest is the file just written.

When the subagent returns, continue to Step 7.

### Step 12: Dispatch the re-reviewer

Write `$IMPLEMENT_DIR/dispatch/<name>.md` — a manifest, per [2.4 Manifests](#24-manifests):

```text
Role: re-reviewer
Mode: intermediate|final
Spec: $SPEC_FILE
Scope: <current-scope-absolute-path>
Output: $IMPLEMENT_DIR/results/<name>.md

Evidence sources:
- <absolute-evidence-path>
Finding sources:
- <defining-result-path>#F<n>
First finding ID: F<n>

REREVIEW_TARGET:
Path: <canonical-absolute-path>
Branch: kiko/$SPEC_SLUG
Base: <full-sha>
Reviewed head: <full-sha>
Head: <full-sha>
END_REREVIEW_TARGET
```

`Mode` is `final` for a final phase, otherwise `intermediate`. `Evidence sources` list the current phase's adopted evidence, DONE and BLOCKED alike. `Finding sources` are the same wave the round's fixer received; `Base` and `First finding ID` follow Step 9's rules. Repeat `REREVIEW_TARGET` per repository any adopted evidence reports: `Reviewed head` is that repository's Head in the triggering result's manifest, or equal to `Base` where this round touches it first; `Head` is its Head in the latest adopted evidence reporting it. `Base..Reviewed head` is already reviewed; `Reviewed head..Head` is the fix delta.

Append this record to `$PROGRESS_FILE`:

```text
Phase P<n>: re-review — dispatch/<name>.md
```

Then dispatch a fresh subagent with the prompt in [2.5 Dispatch prompt](#25-dispatch-prompt); its role contract is `references/re-reviewer.md` and its manifest is the file just written.

When the subagent returns, continue to Step 7.

### Step 13: Route the re-review result

- `clean` — the fix round resolved its wave: for an intermediate phase, continue to Step 5 to plan the next phase; for a final phase, the run is Complete: continue to Step 17.
- `issues` with any Critical or Important finding, or on a final phase — put the choice to the user. Read and follow [references/finding-gate.md](references/finding-gate.md): it ends by opening the next fix round at Step 11, following this step's `clean` route after a full risk acceptance, routing a spec issue through Step 14, or ending the run at Step 18.
- `issues` with only residual or new Minor findings on an intermediate phase — fixing defers: the phase completes and the findings carry as open findings; continue to Step 5 to plan the next phase.
- A reported spec issue or external blocker outranks status routing — handle only the highest-priority reported category, in this order: spec issue (Step 14), then external blocker (Step 15); never route the result's lower-priority conclusions directly.

### Step 14: Handle a spec issue

Any role may report a contradiction in the current spec, a false premise in it, or an acceptance rule that cannot determine correctness. Pause product implementation; the reporting result is the issue source. Read and follow [references/spec-amendment.md](references/spec-amendment.md): it ends either by dispatching a successor through the reporting role's dispatch step, or by adopting an amendment and continuing to Step 5.

### Step 15: Handle an external blocker

The reporting result names something outside the repositories that blocks progress and the user action that clears it. Show the user both and ask whether they will act now.

- Cleared — return to the reporting role's dispatch step and dispatch a successor with [the successor fields](#successor-fields): `Prior result` names the reporting result; a write role whose remaining work is only external verification takes `Action: evidence-recovery` with `Recovery evidence` naming the same result.
- Not now — if open work the blocker does not reach remains, continue to Step 5: write a replacement scope for the still-active phase from that work; the blocked items stay open for a later phase. Otherwise continue to Step 18.

### Step 16: Handle a code blocker

The reporting write role hit a technical obstacle it could not clear; its result records the partial state and a concrete next step. Return to that role's dispatch step and dispatch one successor with [the successor fields](#successor-fields): `Prior result` names the blocked result. A fresh attempt holding the predecessor's recorded state and next step either clears the obstacle or confirms it.

If the successor's result reports the same obstacle, it is confirmed: show the user the obstacle and the reported next step, and offer the choice — if open work the obstacle does not reach remains, park it and continue to Step 5 (a replacement scope for the still-active phase; the blocked items stay open), or end the run (Step 18). A different code blocker is a fresh report where the successor advanced any Head: handle it from the top of this step; with no Head advanced, it is confirmed as well.

### Step 17: Complete

Ask the user how to land the work: in every repository the run touched, merge `kiko/$SPEC_SLUG` into the branch it was created from — its first reporting evidence's `Created from`, or the branch the user names where no adopted evidence records it — and delete it, or keep the branch as is. Do as they choose.

Tidy `$KIKO_ROOT/TODO.md`: delete the tasks this run completed.

Report Complete to the user: only the key outcomes, brief and to the point. The run ends here.

### Step 18: Incomplete

The run stops before every obligation is proven.

Tidy `$KIKO_ROOT/TODO.md`: leave this spec exactly one task line — `- <unfinished goal> — next: <user action> — resume: /implement $SPEC_POINTER`; if the user abandoned the objective, delete its tasks instead. Revert nothing.

Report Incomplete to the user: the cause, the next action, and the work branch holding the partial work — brief and to the point. The run ends here.

## 2 Concepts

### 2.1 Implementation directory

The implementation directory (`$IMPLEMENT_DIR`) is one spec's durable state. It contains only:

```text
progress.md
scopes/
dispatch/
results/
decisions/
```

Files in the subdirectories are immutable: never edit or overwrite one — supersede it with a new file. Basenames are random, generated with `openssl rand -hex 4`; paired files share one `<name>` — a result with its dispatch, an amendment proposal (`<name>-spec.md`) with its decision.

### 2.2 Progress file

The progress file (`$PROGRESS_FILE`) is an append-only index: each record continues the one before it, and a dispatch record stands even when no result for it is ever adopted. The file contains only these records:

```text
Phase P<n>: scope — scopes/<name>.md
Phase P<n>: implement — dispatch/<name>.md
Phase P<n>: implementation result — results/<name>.md
Phase P<n>: review — dispatch/<name>.md
Phase P<n>: review result — results/<name>.md
Phase P<n>: fix — dispatch/<name>.md
Phase P<n>: fix result — results/<name>.md
Phase P<n>: re-review — dispatch/<name>.md
Phase P<n>: re-review result — results/<name>.md
Phase P<n>: risk acceptance — decisions/<name>.md
Spec amendment — decisions/<name>.md
```

`<name>` is the artifact file's random basename. These letters appear here and throughout the skill:

- `P<n>` — phase number; increments when planning the next phase after the current one completes.
- `F<n>` — finding ID, unique across the run; increments with each new finding a review or re-review defines. A finding's severity is fixed by its defining line.

### 2.3 Scope

A scope is one phase's task statement. It has this fixed shape:

```md
# P<n> — <observable outcome>

## Requirements

- <R IDs or exact requirement names, or (none)>

## Acceptance

- <A IDs or exact criterion names, or (none)>

## Open findings

- <absolute-result-path>#F<n>, or (none)

## Landing

<stable, compatible state that must hold when this phase ends>

## Verify

<behaviors and integration results that must be proven>
```

Use only IDs when the spec supplies them; otherwise use exact names. `Landing` states the stable stop boundary and `Verify` states required proof without prescribing commands. The scope contains only the five sections above.

For a final phase, list every remaining R/A and finding explicitly; when open work is the complete current spec, write `the complete current spec` in Requirements and Acceptance instead. Its `Landing` covers the integrated product state and `Verify` includes phase-specific plus whole-spec integration/regression proof. Final review always covers the current whole spec.

### 2.4 Manifests

A manifest is one dispatch's complete, immutable task statement — everything the fresh role receives beyond its role contract.

#### Successor fields

```text
Prior result: <absolute-result-path>
Prior disposition: spec-issue-rejected
Recovery evidence: <absolute-evidence-path>
```

Any manifest may append these. `Prior result` names the result this successor continues — after a code blocker, an external blocker, or a rejected spec issue; only the last adds the fixed `Prior disposition` line. A write role carries `Recovery evidence` exactly when `Action` is `evidence-recovery`.

### 2.5 Dispatch prompt

Dispatch every role as a fresh subagent with exactly this prompt:

```text
Follow the role contract:
<absolute-role-contract-path>

Execute the manifest:
<absolute-manifest-path>

Write the result to the manifest's Output path.
Return only `RESULT: <path>`, or `ERROR: <reason>` if the manifest cannot be safely executed or a complete result cannot be written.
```
