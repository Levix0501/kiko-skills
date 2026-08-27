---
name: create-spec
description: Turn a settled outcome into a build-ready spec a fresh context can implement and verify. Use when the user asks to create a spec.
---

## Create

The spec records a settled outcome: while material decisions remain open, recommend `/kick-off` rather than deciding them yourself.

1. Run the bundled `scripts/check-kiko` with the opened project root's absolute path as its sole argument, not the current shell directory, and treat the returned path as `KIKO_ROOT`. Exit 3 means the layout is missing or incomplete: ask the user to run `/setup-kiko` at the project root, then retry. For any other nonzero exit, report the error and stop. Never initialize or repair `.kiko` yourself.
2. Set the new spec path to `$KIKO_ROOT/specs/YYYY-MM-DD-<topic>.md`, dated today. If that path already exists, choose a different `<topic>`.
3. Write the spec for the settled outcome to the new path using [assets/spec-template.md](assets/spec-template.md), checking workspace facts only where needed to keep the requirements and acceptance accurate.
4. Run [references/self-review.md](references/self-review.md) and fix issues.
5. Give the user the path, necessary choices made while writing, and any high-risk contracts to review closely. Apply requested changes to the same file, self-review the affected scope, and repeat until explicit approval.

After explicit approval, make no semantic change. If the spec is inside a Git worktree, commit only the spec and report the commit SHA. Otherwise warn the user that the spec is local-only. Recommend starting implementation with `/implement <actual-spec-path>` in a new window or after `/compact`, so the build starts without the accumulated conversation.
