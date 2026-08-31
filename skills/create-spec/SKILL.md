---
name: create-spec
description: Turn a settled outcome into a build-ready spec a fresh context can implement and verify. Use when the user asks to create a spec.
---

`create-spec` turns the settled input into a build-ready spec — the delivery
contract a fresh context can implement and verify.

## 1. Locate the target

Run `scripts/check-kiko` with the opened project root's absolute path as its
only argument, and use the returned path as `KIKO_ROOT`.

- Exit 3: ask the user to run `/setup-kiko` at the project root, then retry.
- Any other nonzero exit: report the error and stop.

Do not initialize or repair `.kiko`. Set the spec path to
`$KIKO_ROOT/specs/YYYY-MM-DD-<topic>.md` using today's date. If the path
exists, choose another topic instead of overwriting it.

## 2. Classify the input

Apply [the spec rules](references/spec-rules.md) to each candidate by
semantic role, regardless of its original heading.

Run each empirical premise through the evidence gate in the spec rules before
using it.

## 3. Draft and preflight

Assemble the complete draft per [the template](assets/spec-template.md)
without writing the target file.

Run [the self-review](references/self-review.md) and fix every failure. Write
the complete draft to the target path only after this preflight passes.

## 4. Review with the user

Present the written file as the exact review target, list every D or state that
there are none, and ask the user to confirm or revise the document.

Apply requested changes to the same file. Reclassify the affected content,
rerun any newly relevant evidence gate, and repeat the self-review for affected
items, terms, and references before requesting confirmation again.

Do not put `Draft`, `Approved`, or another approval-status field in the spec or
an auxiliary file.

## 5. Commit the confirmed version

Do not commit until the user explicitly confirms the current file. Any later
semantic change requires another confirmation.

If the spec is in a Git working tree, commit only that file and report the
commit SHA. Otherwise, warn the user that the file carries no confirmation
marker: without a commit, nothing distinguishes it from an abandoned draft.

Recommend starting `/implement <actual-spec-path>` in a new window or after
`/compact`, so the build does not spend context and tokens on the accumulated
conversation.
