---
name: implement
description: Turn a spec into reviewed, shippable code. Use when the user asks to build a spec.
---

Orchestrate implementation from a spec: the controller dispatches fresh roles (subagents) to implement and review. The controller never implements, reviews, or fixes anything itself.

## 1 Workflow

Start with Step 1.

### Step 1: Check kiko workspace

Set `PROJECT_ROOT` to the opened project root's absolute path. Run `scripts/check-kiko` with `$PROJECT_ROOT` as its only argument, and use the returned path as `KIKO_ROOT`.

- Exit 3: ask the user to run the `setup-kiko` skill at the project root, then retry.
- Any other nonzero exit: report the error and stop.

Do not initialize or repair `.kiko`. On success, continue to Step 2.

### Step 2: Resolve spec

Resolve `SPEC_DIR`, the absolute path of a spec directory directly under `$KIKO_ROOT/docs`, from the first available source:

1. the path named when this skill was invoked;
2. the spec directory this conversation is already working on;
3. ask which spec to build.

Then continue to Step 3.

### Step 3: Prepare the implementation directory

Run `scripts/prepare-implement-dir` with `$KIKO_ROOT` and `$SPEC_DIR` as its arguments, and use the returned path as `IMPLEMENT_DIR`, per [2.1 Implementation directory](#21-implementation-directory). On a nonzero exit, show the error and ask the user to fix what it names, then retry from Step 2.

On success, derive:

```sh
SPEC_SLUG=$(basename "$SPEC_DIR")
SPEC_FILE="$SPEC_DIR/spec.md"
FACTS_FILE="$SPEC_DIR/facts.md"
DECISIONS_FILE="$SPEC_DIR/decisions.md"
```

- `$FACTS_FILE` — the roles' `Facts`, per [references/facts.md](references/facts.md).
- `$DECISIONS_FILE` — the decisions made so far toward the outcome, per [2.5 Decisions](#25-decisions).

Then check every repository at or under `$PROJECT_ROOT` for uncommitted content: the roles create the work branch from the current branch, so anything left uncommitted would end up in their work. If any tree is not clean, show the user what you found right away, before looking into anything else, and offer the choice:

- the agent commits it as it stands, one commit per repository on its current branch;
- the user commits, stashes, or discards it themselves and tells the agent when every tree is clean;
- the run ends (Step 14).

Then show the user, for each repository, the branch the work branch will be created from, its head, and how far it is ahead of or behind its upstream: reviewers see only `Base..Head`, so nothing later revisits where `Base` came from.

Then continue to Step 4.

### Step 4: Read progress file

```sh
PROGRESS_FILE="$IMPLEMENT_DIR/progress.md"
```

`$PROGRESS_FILE` is the run's index, per [2.2 Progress file](#22-progress-file). Create it empty if it does not exist. If it is empty, continue to Step 5. If its last record is `Complete`, tell the user this spec is already built and stop. Otherwise resume: read the records and the artifacts they point to, determine which step the run reached, and continue from that step.

### Step 5: Dispatch the implementer

Write `$IMPLEMENT_DIR/dispatch/<name>.md` — a manifest, per [2.3 Manifests](#23-manifests):

```text
Role: implementer
Branch: kiko/$SPEC_SLUG
Spec: $SPEC_FILE
Facts: $FACTS_FILE
Output: $IMPLEMENT_DIR/results/<name>.md
```

`Branch` is the spec's work branch: in every repository it modifies, the role works on this branch, creating it from the repository's current branch on first touch.

Append this record to `$PROGRESS_FILE`:

```text
Implement — dispatch/<name>.md
```

Then dispatch the role per [2.4 Dispatch](#24-dispatch); its role contract is `references/implementer.md` and its manifest is the file just written.

When the subagent returns, continue to Step 6.

### Step 6: Read the subagent result

Read the result at the manifest's `Output` path. If the subagent returns `ERROR:` or the result is incomplete, determine how to proceed from the cause and the work already done.

When a complete result is available, adopt it by appending the matching line to `$PROGRESS_FILE`, reusing the dispatch's `<name>`. A review result with a `Fix` section is a review+fix result.

```text
Implementation result — results/<name>.md
Review result — results/<name>.md
Review+fix result — results/<name>.md
```

Then continue by result type: implementation → Step 7; review or review+fix → Step 9.

When a result reports both a spec issue and an external blocker, route by the spec issue.

### Step 7: Route the implementation result

- `DONE` → Step 8.
- `BLOCKED` → spec issue: Step 10; external blocker: Step 11; otherwise Step 12.

### Step 8: Dispatch the reviewer

Write `$IMPLEMENT_DIR/dispatch/<name>.md` — a manifest, per [2.3 Manifests](#23-manifests):

```text
Role: reviewer
Branch: kiko/$SPEC_SLUG
Spec: $SPEC_FILE
Facts: $FACTS_FILE
Output: $IMPLEMENT_DIR/results/<name>.md
Prior result: <absolute-result-path>
First finding ID: F<n>
```

- `Prior result` is the latest adopted result, per [2.6 Results](#26-results): the implementation result to review, or the review result whose fixes to verify.
- `First finding ID` is one greater than the highest finding ID in any adopted review or review+fix result, or `F1` if none.

Append this record to `$PROGRESS_FILE`:

```text
Review — dispatch/<name>.md
```

Then dispatch the role per [2.4 Dispatch](#24-dispatch); its role contract is `references/reviewer.md` and its manifest is the file just written.

When the subagent returns, continue to Step 6.

### Step 9: Route the review result

- `Status: clean` → Step 13.
- `Status: issues` without `Fix` → spec issue: Step 10; external blocker: Step 11.
- `Fix: DONE` whose fixes changed no behavior → Step 13. The fixes changed no behavior when, for every repository the result lists, the bundled `scripts/check-text-only` with the repository path, the `Head` its `Prior result` records for it (its `Base` when the prior result does not list it), and its `Head` exits 0.
- Any other `Fix: DONE` → Step 8, so a fresh reviewer verifies the fixes; when this is the third or a later `Fix: DONE` result since the latest `Implementation result` or `Spec amendment` record, read and follow [references/finding-gate.md](references/finding-gate.md) instead.
- `Fix: BLOCKED` → spec issue: Step 10; external blocker: Step 11; otherwise Step 12.

### Step 10: Handle a spec issue

Read and follow [references/spec-amendment.md](references/spec-amendment.md): whether the spec stands or was amended, it ends by dispatching a successor through the reporting result's [successor dispatch step](#successor-dispatch-step).

### Step 11: Handle an external blocker

The reporting result names something outside the repositories that blocks progress and the user action that clears it. Show the user both and ask how to proceed.

- Cleared — dispatch a successor through the reporting result's [successor dispatch step](#successor-dispatch-step).
- Changed — the user drops, defers, or substitutes the blocked obligation instead.
- Not now — continue to Step 14; the next run resumes here.

For Changed, edit the spec to say what the user decided, keeping the rules the [spec amendment draft](references/spec-amendment.md#draft-and-self-review) follows for identifiers and A coverage; append the `[user]` decision per [2.5 Decisions](#25-decisions) and a `Spec amendment — DECISION<n>` record to `$PROGRESS_FILE`, and commit the spec with the decision when tracked. Give a deferred obligation a task line in `$KIKO_ROOT/TODO.md`. Then dispatch a successor as for Cleared.

### Step 12: Handle a blocked attempt

The role stopped short with neither a spec issue nor an external blocker; its `Remaining` says what stopped it, what was tried, and what is needed. Dispatch one successor through the reporting result's [successor dispatch step](#successor-dispatch-step): a fresh attempt holding that record either finishes or stops short again.

If the successor also stops short, show the user its `Remaining` and ask how to proceed.

- Continue — dispatch another successor, after the user acts on what is needed if they can.
- Changed — the user drops, defers, or substitutes the obligation instead: proceed as for Changed in Step 11.
- Not now — continue to Step 14; the next run resumes here.

### Step 13: Complete

Ask the user how to land the work in every repository the run touched, the ones in the latest adopted result's repository blocks:

- the agent merges `kiko/$SPEC_SLUG` into the branch it was created from — its `Created from`, or the branch the user names where no result records it — and deletes it;
- the agent pushes the branch for the user to open a pull request;
- the branch stays as it is.

Do as they choose.

Append `Complete` to `$PROGRESS_FILE`.

Report Complete to the user: the key outcomes and every Concern in adopted implementation results that names a decision the user has not made, brief and to the point. The run ends here.

### Step 14: Incomplete

The run stops before every obligation is proven. Revert nothing.

Report Incomplete to the user: the cause, the next action, the work branch holding the partial work, and that a later run on the same spec resumes from here — brief and to the point. The run ends here.

## 2 Concepts

### 2.1 Implementation directory

The implementation directory (`$IMPLEMENT_DIR`) is one spec's run state: it persists across invocations and is not committed. It contains only:

```text
progress.md
dispatch/
results/
```

Files in the subdirectories are immutable: never edit or overwrite one — supersede it with a new file. Basenames are random, such as `openssl rand -hex 4` gives; a result shares its dispatch's `<name>`.

### 2.2 Progress file

The progress file (`$PROGRESS_FILE`) is an append-only index in order: the last record is where the run stands, and a dispatch record stands even when no result for it is ever adopted. The file contains only these records:

```text
Implement — dispatch/<name>.md
Implementation result — results/<name>.md
Review — dispatch/<name>.md
Review result — results/<name>.md
Review+fix result — results/<name>.md
Risk acceptance — DECISION<n>
Spec amendment — DECISION<n>
Complete
```

### 2.3 Manifests

A manifest is one dispatch's complete, immutable task statement — everything the role receives beyond its role contract.

#### Prior result

```text
Prior result: <absolute-result-path>
Prior disposition: spec-issue-rejected
```

`Prior result` names the latest adopted result, the one the role starts from per [2.6 Results](#26-results). Every reviewer manifest carries it; an implementer manifest carries it as a successor after a blocked attempt, an external blocker, or a spec issue, whether rejected or adopted as an amendment. Only the rejected case adds the fixed `Prior disposition` line.

#### Successor dispatch step

A successor is dispatched through the step matching the reported result's type: an implementation result through Step 5, a review or review+fix result through Step 8.

### 2.4 Dispatch

Dispatch every role as a fresh subagent with exactly this prompt:

```text
Follow the role contract:
<absolute-role-contract-path>

Execute the manifest:
<absolute-manifest-path>

Write the result to the manifest's Output path.
Return only `RESULT: <path>`, or `ERROR: <reason>` if the manifest cannot be safely executed or a complete result cannot be written.
```

One exception. A successor after a spec issue or an external blocker whose role matches the subagent that wrote its `Prior result` continues that subagent, sending it the same prompt with the new manifest, when the harness can resume a returned subagent with its context intact — in Claude Code, a message to the agent by name. When it cannot, or that subagent no longer exists, dispatch a fresh subagent. A successor after a blocked attempt is always a fresh subagent.

### 2.5 Decisions

`$DECISIONS_FILE` holds one entry per decision made toward the outcome. The entries before this run were written while the spec was formed; during the run only the controller appends, on a spec amendment or a risk acceptance, and commits what it appended when the spec directory is tracked in Git: the write roles refuse a dirty tree. Append-only, never edited or deleted; a changed decision is a new entry that says which ID it supersedes. Numbers in `DECISION<n>` increase and are never reused.

```md
- DECISION<n> [user] <question>
  <answer>
- DECISION<n> [controller] <question>
  <answer>
```

`[user]` records the user's decision, in their words where given; `[controller]` records a decision the controller made itself. The facts a decision rests on are appended to `$FACTS_FILE`, `verified` for what the controller established and `user` for what the user supplied, and named in the answer. Question and answer together have one reading for someone with no run history. Name the R, D, A, or O concerned, and quote a finding rather than only citing `F<n>`: `.implement/` is not committed, so the finding cannot be looked up later. No role receives this file.

### 2.6 Results

Results are cumulative: a role reads only its `Prior result`, and the controller takes repository state from the latest adopted result. Every result carries forward the repository blocks of its `Prior result` — every repository on the work branch, with `Base` the commit the branch was created from, `Created from` where recorded, and `Head` updated to the current commit — and adds any repository this dispatch first touched. A review result also carries forward the findings its `Prior result` lists. Everything else in a result is that dispatch's own report.
