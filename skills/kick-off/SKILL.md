---
name: kick-off
description: A relentless interview that turns an unclear request into a shared, actionable understanding.
disable-model-invocation: true
---

## Objective

Before detailed design questions, determine whether the request is one coherent outcome or several independent outcomes.

Treat outcomes as independent only when each can be approved, delivered, and evolved without the others.

If there is one outcome, state it briefly and continue. If there are several, name them, recommend one to focus on, and ask the user to choose.

After the user chooses, run the bundled `scripts/check-kiko` with the opened project root's absolute path as its sole argument, not the current shell directory, and treat the returned path as `KIKO_ROOT`. Exit 3 means the layout is missing or incomplete: ask the user to run `/setup-kiko` at the project root, then retry. For any other nonzero exit, report the error and stop. Never initialize or repair `.kiko` yourself. Then add the unselected outcomes to `$KIKO_ROOT/TODO.md` in simple, unrefined form.

## Interview

Interview the user relentlessly until you reach a shared understanding. Map this as a design tree: every decision branches into the decisions that hang off it.

Work the tree one question at a time. The frontier is every decision whose prerequisites are already settled: the questions you can ask now without guessing at answers you haven't heard yet.

Choose one question from the frontier and ask it with AskUserQuestion. Put your recommended option first and explain its main reason and cost. Then wait for the user's response before asking another question.

Each answer reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. Recompute the frontier and ask the next question. A question whose answer depends on an unsettled decision is not yet in the frontier.

Facts discoverable from the environment are your job to find, not the user's. Ask the user for decisions and facts only they can provide. When a frontier question needs a fact from the environment, look it up instead of asking the user. A running exploration is an unsettled prerequisite, so questions downstream of it wait; ask another question from the frontier when one is available.

The interview is done when the frontier is empty: every branch of the design tree has been visited and nothing remains silently assumed. Do not act on the result until the user confirms you have reached a shared understanding.

## Summary

When the frontier is empty, present a concise summary covering:

- the objective
- what is in and out of scope
- the requirements and decisions that affect implementation
- the acceptance criteria
- any relevant constraints, edge cases, or failure behavior

## Next action

After presenting the summary, ask what to do next:

- create a spec
- start implementing directly

Recommend one option, put it first, and explain the main reason and cost.

Recommend a spec when the work involves irreversible changes, migration, security, permissions, payment, public or external contracts, coordination across systems or sessions, a substantial acceptance matrix, or a strong need for durable review and recovery.

Recommend direct implementation when the work is local, reversible, can be completed in the current session, and the summary is a sufficient requirements record. Do not decide based on size or file count alone.

Selecting either option confirms the summary.

If the user selects `create a spec`, invoke the `create-spec` skill with the confirmed summary and continue. If `create-spec` is unavailable, tell the user that the skill is not installed and cannot be invoked; do not silently substitute another spec-writing flow.

If the user selects `start implementing directly`, begin implementation immediately in the current context, using the confirmed summary as the requirements record.

If the user enters a custom response instead, treat it as continued feedback, not confirmation. Answer questions, investigate facts, revise the design tree, or resume the interview as needed. When the frontier is empty again, present the updated summary and ask the same next-action question again.

Do not create the spec or start implementation until the user explicitly selects an action or clearly requests it in their own words.
