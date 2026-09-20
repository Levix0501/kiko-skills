# Implementation Result template

Use this template when writing or reading an implementation result.

## Template

```markdown
# Implementation Result

Status: DONE|BLOCKED

## Repositories

### <repository-name>

Path: <canonical-absolute-path>
Branch: <work branch>
Created from: <source branch, recorded by the dispatch that created the work branch and carried forward>
Base: <full-sha the work branch was created from>
Head: <full-sha at the end of this dispatch>

## Verification

- <repository or behavior>: <command or verification method and result>

## Facts

<IDs of facts appended>

## Concern

- <concrete non-blocking concern relevant to review>

## Spec issue

<affected Spec requirement or text, the problem, and its root cause>

## External blocker

<affected requirement, unavailable external condition, and action needed;
include the owner and confirmation method when useful>

## Remaining

<unfinished or unverified work, what stopped you, what you tried, and what is needed to continue>
```

## Field notes

- `DONE` means the complete Spec is implemented, verified, committed, and clean. Omit `Spec issue`, `External blocker`, and `Remaining`.
- `BLOCKED` means work remains incomplete. Include `Remaining` and the applicable issue sections.
- Under `Repositories`, carry forward every block from `Prior result`, updating `Head`, and add a third-level heading for each repository this dispatch first touched on the work branch, including one it left without a commit. `Base` is the commit the work branch was created from and never changes; `Head` is the current commit.
- `Verification` combines repository tests, integration checks, and acceptance verification. Associate each check with its repository or behavior and report the method and concise result.
- Omit `Facts` and `Concern` when there is nothing to report. Include each issue section only when applicable.
