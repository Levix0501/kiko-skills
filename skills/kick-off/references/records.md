# Decisions and facts

Set `SLUG` to `YYYY-MM-DD-<topic>`: today's date and a kebab-case topic
taken from the outcome. Pick another topic if `$KIKO_ROOT/docs/$SLUG`
exists. Create the directory.

## decisions.md

`$KIKO_ROOT/docs/$SLUG/decisions.md` is the record of what the user
committed to about the outcome. Append-only, never edited or deleted; a
changed decision is a new entry that says which ID it supersedes.

```md
# Decisions — <slug>

- DECISION1 [user] Outcome
  <who, what changes, why>
- DECISION<n> [user] <question, with the options as put>
  <what the user chose or said>
```

Numbers in `DECISION<n>` increase and are never reused; `[user]` marks the
user as the decider. An entry is one act of the user: a question put to
them and their answer, or their own words. It holds what they saw and what
they said, nothing written for them; confirming the summary is not such an
act. A requirement the user imposes and a risk they accept are decisions
too; a choice they leave open is an entry whose answer says so. Question
and answer together have one reading for someone with no conversation
history.

Write one entry per settled decision.

## facts.md

`$KIKO_ROOT/docs/$SLUG/facts.md` is the record of the facts the decisions
rest on: statements about the world, each with the source that shows it.
Append-only, never edited or deleted; a correction is a new line that says
which ID it supersedes.

```md
# Facts — <slug>

- FACT<n> [user] <statement> — bears on: <DECISION IDs>
- FACT<n> [verified: <source>] <statement> — bears on: <DECISION IDs>
```

Numbers in `FACT<n>` increase and are never reused. `user` marks a statement
only the user could supply; `verified` names what established the statement
in this session. A statement says no more than its source shows; what the
user wants of the work is a decision, not a fact. State each fact concretely
enough to be checked later.

Write one line per fact.

## Commit and continue

These files transcribe the summary the user confirmed, and the spec is what
they review next, so ask for no confirmation here.

If the directory is in a Git working tree, commit it and report the SHA.
Then invoke the `create-spec` skill with `$KIKO_ROOT/docs/$SLUG`.
