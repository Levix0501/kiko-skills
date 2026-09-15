# Review Result template

Use this template when writing or reading a review result.

## Template

```markdown
# Review Result

Status: clean|issues

## Repositories

### <repository-name>

Path: <canonical-absolute-path>
Branch: <work branch>
Created from: <source branch, recorded by the dispatch that created the work branch and carried forward>
Base: <full-sha the work branch was created from>
Head: <full-sha at the end of this dispatch>

## Prior findings

- <finding carried forward from Prior result, unchanged>

## Findings

- F<n> | critical|important|minor | <self-contained finding>

## Facts

<IDs of facts appended>

## Spec issue

<affected Spec requirement or text, the problem, and its root cause>

## External blocker

<affected requirement, unavailable external condition, and action needed;
include the owner and confirmation method when useful>

## Fix

Fix: DONE|BLOCKED

### Verification

- <repository or behavior>: <command or verification method and result>

### Remaining

<unfinished or unverified work, what stopped you, what you tried, and what is needed to continue>
```

## Field notes

- `Status` describes the review at the reviewed Head: `clean` when there is no finding, spec issue, or external blocker, otherwise `issues`. Fixing does not change it.
- Under `Repositories`, carry forward every block from `Prior result`, updating `Head`, and add a third-level heading for each repository this dispatch first touched on the work branch. `Base` is the commit the work branch was created from and never changes; `Head` is the current commit.
- `Prior findings` are the findings `Prior result` lists under both `Prior findings` and `Findings`, unchanged, so the chain since the latest implementation result stays whole. `Findings` are this review's own, numbered from `First finding ID`.
- Include `Fix` only when the fix stage ran. `Fix: DONE` means every finding is resolved, verified, committed, and clean; omit `Remaining`. `Fix: BLOCKED` means fixing is unfinished; include `Remaining`.
- Omit `Prior findings`, `Findings`, `Facts`, and the issue sections when there is nothing to report.
