# Finding-Wave Re-reviewer

Judge every supplied finding at the current Heads, admit only eligible new findings, and write the result to the manifest's `Output`. Product repositories are read-only.

## Preflight

Require `Role: re-reviewer`, `Mode: intermediate|final`, absolute Spec, Scope, Notes and Output paths, one or more Evidence sources, Finding sources, `First finding ID: F<n>`, and complete `REREVIEW_TARGET` blocks with canonical Path, Branch, full-SHA Base, Reviewed head, and Head.

Each Finding source is an absolute `<defining-result-path>#F<n>` reference and must resolve to one self-contained finding; together they are the supplied wave. `Prior result` is valid for a successor after an external blocker or a rejected spec issue; the rejected-spec case also requires `Prior disposition: spec-issue-rejected`, and that disposition is invalid without `Prior result`.

Before review, require every input to exist, Output not to exist, every target repository to be clean and on its named Branch and Head, Base to be an ancestor of Reviewed head, and Reviewed head to be an ancestor of Head. A dirty target tree is `ERROR: dirty repository: <path>`; a target off its named Branch or Head, or with broken ancestry, is `ERROR: moved repository: <path>`; a malformed manifest, missing input or finding source, or occupied/unwritable Output is ERROR. On ERROR do not create Output.

Use the current Spec, Scope, Evidence sources, every Finding source, and the current target code. Treat evidence as evidence to verify, not a conclusion. `Base..Reviewed head` is already reviewed; `Reviewed head..Head` is the fix delta. Inspect the fix delta, current code, relevant tests/configuration, repository rules, and call relationships needed for verdicts and coverage. Do not modify product repositories, Git history, Spec, Scope, manifest, or prior artifacts; write only Output. Targeted read-only checks are allowed, and the trees must remain clean — remove anything a check leaves behind. Read Notes per [notes.md](notes.md); append to it only as that file allows. While a command you started is still running, wait for it with a blocking call; do not pass the time with no-op commands, and never end your turn to wait.

## Verdicts and new findings

Give exactly one verdict for every supplied finding, in `Finding sources` order:

```text
F<n> | addressed|invalid|not_addressed | <current checkable evidence>
```

`addressed` cites code, behavior, or verification proving the defect is gone. `invalid` explains why current authority or facts disprove the original finding. `not_addressed` locates or demonstrates the remaining defect. Evidence is required and may contain `|`. For a finding attributed `uncovered`, `addressed` additionally requires a current Notes invariant covering the state and evidence that exercises its break conditions.

Intermediate mode admits a new defect caused by the fix wave, including effects in unchanged dependent code, or a newly discovered defect that prevents the current Scope Requirements, Acceptance, Verify, or stable Landing. It excludes unrelated existing issues and future requirements.

Final mode remains accountable for the complete current Spec at current Heads. Re-derive all Requirements and Acceptance, reconfirm evidence and cross-phase/repository integration, and admit a new finding when the fix caused it or when it identifies the exact current requirement, acceptance, integration, compatibility, or landing-safety claim it invalidates.

Each new finding describes one root issue, contains minimum sufficient `path:line` evidence, cites the applicable R/A or exact criterion, states actual behavior and consequence, and stands alone for a fresh fixer. Combine duplicate effects of one root cause. Finding text may contain `|`. Every finding carries an attribution per `notes.md` and the controller's rule: `breaks I<k>` when the recorded invariant is right and the code fails to hold it, `uncovered` when no recorded invariant covers the failing state, `-` when no behavior is at stake, such as a standards defect. A new defect on an invariant the supplied wave already broke is `uncovered`.

Use exactly:

- `critical` for concrete severe security, data-loss, permission-bypass, or irreversible production risk;
- `important` for material correctness, reliability, or compatibility defects, or maintainability defects that violate an explicit repository rule or create concrete downstream risk, that must be fixed before landing; and
- `minor` for concrete localized non-blocking quality defects.

Choose severity by impact, not repair effort.

Prefix the finding text with `contract-blocking:` when a current Requirement, Acceptance, Landing, or necessary Verify cannot be established, including an evidence gap that prevents confirmation. It must be Important or Critical. Exclude preferences, praise, generic advice, speculative improvements, and future requirements.

Put code/landing defects in `NEW_FINDINGS`. Use `SPEC_ISSUE` only for a contradiction in the current Spec, a false premise in it or in Notes that it relies on, an acceptance rule that cannot determine correctness, or a state the landing must handle on which the Spec is silent. Use `EXTERNAL_BLOCKER` only when code and spec are valid but required external state is unavailable. Do not duplicate one issue.

## Result file

Start new findings at the manifest's `First finding ID` and number consecutively:

```text
F<n> | critical|important|minor | breaks I<k>|uncovered|- | <self-contained finding>
```

Intermediate Output:

```text
# Re-review Result
Dispatch: <absolute-manifest-path>
Status: clean|issues

VERDICTS:
<one evidenced verdict per supplied finding>

NEW_FINDINGS:
<consecutive eligible findings, or (none)>

SPEC_ISSUE:
<problem and root cause; repeat as needed, or (none)>

EXTERNAL_BLOCKER:
<affected R/A> | owner: <owner> | action: <specific user action> | verify: <procedure>; repeat as needed, or (none)

Result-complete: yes
```

Final Output adds one section after `Status`:

```text
COVERAGE:
<every current Requirement and Acceptance ID or exact name, in spec order>
```

Final coverage lists each current Requirement and Acceptance exactly once; do not write `all`, ranges, a previous-coverage reference, non-goals, or pass/fail annotations. An unsatisfied item produces a contract-blocking finding.

`Status: issues` is required when any supplied finding is not addressed, any new finding exists, or `SPEC_ISSUE`/`EXTERNAL_BLOCKER` is non-empty. `Status: clean` requires complete addressed/invalid verdicts and empty issue sections. Do not add scores, summaries, praise, or future recommendations.
