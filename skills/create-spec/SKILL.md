---
name: create-spec
description: Turn a settled outcome into a build-ready spec a fresh context can implement and verify. Use when the user asks to create a spec.
---

`create-spec` turns the settled input into a build-ready spec — the delivery
contract a fresh context can implement and verify.

## 1. Locate the spec directory

Run `scripts/check-kiko` with the opened project root's absolute path as its
only argument, and use the returned path as `KIKO_ROOT`.

- Exit 3: ask the user to run the `setup-kiko` skill at the project root,
  then retry.
- Any other nonzero exit: report the error and stop.

Do not initialize or repair `.kiko`.

`SPEC_DIR` is the spec directory named when this skill was invoked, directly
under `$KIKO_ROOT/docs`; ask for it when none was named. It holds
`decisions.md`, the decisions made so far toward the outcome, and `facts.md`,
per [the facts file](references/facts.md). Together they are the settled
input. The spec goes to `$SPEC_DIR/spec.md`; if it already exists, report
that and stop.

## 2. Classify the input

`decisions.md` holds one entry per decision:

```md
- DECISION<n> [user] <question>
  <answer>
```

`DECISION1` is the outcome and becomes the Goal. `[user]` marks the user as
the decider. IDs increase and are never reused; a changed decision is a new
entry that says which ID it supersedes. A decision the user makes while
`create-spec` runs, whether answering a returned issue or revising the
draft, is appended the same way before it is used, worded so that question
and answer have one reading without the conversation.

Apply [the spec rules](references/spec-rules.md) to each entry. Run each
empirical premise through the evidence gate in the spec rules before using
it.

## 3. Draft and preflight

Assemble, without writing either file, the complete draft per
[the template](assets/spec-template.md) and the facts to append per
[the facts file](references/facts.md).

Run [the self-review](references/self-review.md) and fix every failure. Write
the draft to `$SPEC_DIR/spec.md` and append the facts to `$SPEC_DIR/facts.md`
only after this preflight passes.

## 4. Review with the user

Present the written file as the exact review target, list every D or state
that there are none, show the decisions entries added since the input, name
the entries the delivery boundary left to the user's release, and ask the
user to confirm or revise the document.

Treat a revision like the first draft: classify, gate, and self-review what
changed, then ask again.

## 5. Commit the confirmed version

Do not commit until the user explicitly confirms the current file. Any later
semantic change requires another confirmation.

If the spec directory is in a Git working tree, commit it and report the
commit SHA; otherwise warn the user that nothing marks the spec as confirmed.

Recommend starting `/implement $SPEC_DIR` in a new window or after
`/compact`, so that implement starts from the files rather than this
conversation.
