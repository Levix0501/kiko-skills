---
name: setup-kiko
description: Initialize or repair the project-local .kiko workspace at the project root.
disable-model-invocation: true
---

Run the bundled `scripts/init-kiko <project-root>` with the opened project root and report the path it prints. The script is idempotent: it creates any missing part of `.kiko/{TODO.md,docs/,.implement/,.gitignore}` while preserving existing artifacts and custom ignore content, so rerunning it also repairs an incomplete layout.

Initialize the root the user intends to work in — not a subdirectory and not a parent that happens to contain it. If the opened directory is ambiguous, ask the user to confirm the root first.

If the script warns that the root is not inside a Git worktree, tell the user Git has not been initialized there and the kiko artifacts are local-only; never run `git init` for them.

If the script exits with an error, show its message and stop; never create or repair `.kiko` by hand.
