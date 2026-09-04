# Notes

One notes file accompanies each spec: `$KIKO_ROOT/notes/<spec basename>`. It holds what the spec excludes and the code cannot state: established facts, and the invariants by which the code satisfies the spec.

The file is not authoritative: the spec prevails over it, and observed repository or runtime state prevails over it. It is append-only: never edit or delete a line; a later line with the same ID supersedes the earlier one, and the latest line for an ID is current.

## Format

```md
# Notes — <spec basename>

## Facts

- K<n> [verified: <source>|user|assumed] <statement> — bears on: <R/D IDs, or ->

## Invariants

- I<n> (<R/D IDs>) <statement> — holds at: <path or symbol>; breaks when: <conditions>; evidence: <verification that exercises each condition>
```

`K<n>` and `I<n>` are unique within the file and never reused. `verified` names the command, `path:line`, result file, or observation that established the statement in the writer's own session; `user` marks a statement the user supplied; `assumed` marks everything else. `bears on` lists the R/D whose meaning or readiness depends on the fact; such a fact is a premise of the spec. A correction or revision is a new line with the same ID.

An invariant is one mechanism, stated so that it can be broken: `breaks when` enumerates the conditions under which the mechanism stops holding, and `evidence` names verification that exercises those conditions. One invariant may carry several R/D.

## Who writes

- `create-spec`: the facts and premises the spec relies on.
- implementer: before DONE, the invariants by which the landing satisfies each scoped Requirement and binding decision.
- a fixer at `Level: model`, and a reviewer whose fix wave is model-level: the revised or new invariant, before changing code.
- any role: a fact it established or disproved during its work.

A reader treats a recorded invariant as a claim to attack and a recorded measurement as a value to re-measure.
