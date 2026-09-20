# Facts

`Facts` is the spec directory's `facts.md`, the file the manifest names as `Facts`. It holds what the spec excludes and the code cannot state: the facts the spec and the work rest on. It is not authoritative: the spec prevails over it, and observed repository or runtime state prevails over it. It is append-only: never edit or delete a line.

```md
# Facts — <slug>

- FACT<n> [user] <statement> — bears on: <DECISION, R, or D IDs>
- FACT<n> [verified: <source>] <statement> — bears on: <DECISION, R, or D IDs>
```

`user` marks a statement the user supplied; `verified` names the command, `path:line`, result file, or observation that established the statement in the writer's own session. `bears on` lists the DECISION, R, or D whose meaning or readiness depends on the fact; such a fact is a premise of the spec. Numbers in `FACT<n>` increase and are never reused; a correction is a new line that says which ID it supersedes.

A role appends a fact it established or disproved and commits it, on its own when nothing else changed: the file lives in the committed spec directory, and a dirty tree stops the next role.

A fact was true when it was written; re-check it before relying on it.
