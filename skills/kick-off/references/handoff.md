# Handoff to create-spec

Set `SLUG` to `YYYY-MM-DD-<topic>`: today's date and a kebab-case topic
taken from the outcome. Pick another topic if `$KIKO_ROOT/docs/$SLUG`
exists. Create the directory.

## decisions.md

`$KIKO_ROOT/docs/$SLUG/decisions.md` is the record of what the user decided.
Append-only, never edited or deleted; a changed decision is a new entry that
says which ID it supersedes.

```md
# Decisions — <slug>

- DECISION1 [user] Outcome
  <who, what changes, why>
- DECISION<n> [user] <question>
  <answer>
```

Numbers in `DECISION<n>` increase and are never reused; `[user]` marks the
user as the decider. An entry exists only for a decision the user actually
made; a choice the user left open is an entry whose answer says so. Question and answer together have one reading for someone with no
conversation history. The user's reason, when given, stays in their words.

Write one entry per settled decision. Do not add what the user did not
confirm.

## facts.md

`$KIKO_ROOT/docs/$SLUG/facts.md` is the record of facts only the user could
supply. Append-only, never edited or deleted; a correction is a new line
that says which ID it supersedes.

```md
# Facts — <slug>

- FACT<n> [user] <statement> — bears on: <DECISION IDs>
```

Numbers in `FACT<n>` increase and are never reused. State each fact
concretely enough to be checked later. What you verified yourself from the environment is not
recorded.

Write one line per fact the user supplied.

## Confirm, commit, hand off

Show the user both files and ask whether they are faithful. A changed mind
is a new entry that says which ID it supersedes.

If the directory is in a Git working tree, commit it and report the SHA.

Invoke the `create-spec` skill with `$KIKO_ROOT/docs/$SLUG`. From here the
files are the settled input; the conversation is not.
