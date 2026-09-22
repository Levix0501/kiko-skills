# Facts

`$SPEC_DIR/facts.md` holds the empirical facts the decisions and the spec
rest on, one line each. It is not authoritative: the spec prevails over it,
and observed repository or runtime state prevails over it. Append-only,
never edited or deleted; a correction is a new line that says which ID it
supersedes.

```md
# Facts — <slug>

- FACT<n> [user] <statement> — bears on: <DECISION, R, or D IDs>
- FACT<n> [verified: <source>] <statement> — bears on: <DECISION, R, or D IDs>
```

Numbers in `FACT<n>` increase and are never reused. `user` marks a statement
only the user could supply; `verified` names what established the statement
in the writer's own session. A statement says no more than its source shows,
and one without a source is not a fact; what the user wants of the work is a
decision, not a fact. `bears on` lists the decisions, R, or D whose meaning
or readiness depends on the fact.

`create-spec` appends every premise the evidence gate admitted, `verified`
with its source, and every fact the user supplies while it runs, `user`; each
bears on the decisions, R, or D it affects.
