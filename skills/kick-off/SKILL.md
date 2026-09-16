---
name: kick-off
description: A relentless interview that turns an unclear request into a shared, actionable understanding.
disable-model-invocation: true
---

## Workspace

Run `scripts/check-kiko` with the opened project root's absolute path as its only argument, and use the returned path as `KIKO_ROOT`. On exit 3, ask the user to run the `setup-kiko` skill at the project root, then retry. On any other nonzero exit, report the error and stop. Do not initialize or repair `.kiko`, and suggest no substitute for `setup-kiko`.

## Objective

Before detailed design questions, determine whether the request is one coherent outcome or several independent outcomes.

Treat outcomes as independent only when each can be approved, delivered, and evolved without the others.

An outcome ends at the repositories: how the work is then pushed or deployed is the user's own release, not a decision to settle here.

If there is one outcome, state it briefly and continue. If there are several, name them, recommend one to focus on, and ask the user to choose.

After the user chooses, add the unselected outcomes to `$KIKO_ROOT/TODO.md` in simple, unrefined form.

## Interview

Interview the user until every decision is settled. Model the work as a design tree: a decision's prerequisites are the upstream decisions and environment facts its options depend on, and its answer opens the decisions below it.

The frontier is every unsettled decision whose prerequisites are all settled. Two frontier decisions never depend on each other, so a dependency chain settles one link per round.

Ask the user only for decisions and for facts only they can provide. Every other fact is a prerequisite you settle yourself from the environment; while a lookup is running, its dependents stay out of the frontier.

Each round is one call of the harness's structured question tool, covering as much of the frontier as its per-call limit allows, starting with the questions that unblock the most. Without such a tool, ask in the reply with numbered options. In each question, put the recommended option first with its main reason and cost. Wait for the answers, update the tree, recompute the frontier, and ask the next round.

The interview ends when the frontier is empty: every decision is settled and nothing is left silently assumed. Do not act on the result until the user confirms the summary.

## Summary

When the frontier is empty, present a concise summary of what was settled:

- the outcome and its boundaries
- the requirements and decisions the work must honor
- the facts relied on, each with how it was established, and the risks accepted

## Next action

After presenting the summary, ask what to do next:

- create a spec
- start implementing directly

Recommend one option, put it first, and explain the main reason and cost.

Recommend a spec when the work involves irreversible changes, migration, security, permissions, payment, public or external contracts, coordination across systems or sessions, a substantial acceptance matrix, or a strong need for durable review and recovery.

Recommend direct implementation when the work is local, reversible, can be completed in the current session, and the summary is a sufficient requirements record. Do not decide based on size or file count alone.

Selecting either option confirms the summary.

If the user selects `create a spec` and the `create-spec` skill is unavailable, tell the user that it is not installed and cannot be invoked; do not silently substitute another spec-writing flow. Otherwise read and follow [references/handoff.md](references/handoff.md): it records the settled decisions and ends by invoking `create-spec`.

If the user selects `start implementing directly`, begin implementation immediately in the current context, using the confirmed summary as the requirements record.

If the user enters a custom response instead, treat it as continued feedback, not confirmation. Answer questions, investigate facts, revise the design tree, or resume the interview as needed. When the frontier is empty again, present the updated summary and ask the same next-action question again.

Do not create the spec or start implementation until the user explicitly selects an action or clearly requests it in their own words.
