# Spec template

```markdown
# <Topic>

## Objective & Scope

<the coherent outcome, why it matters, its owned boundary, and outcome-level
exclusions>

## Requirements

### R1 — <short name>

<one binding, observable obligation>

### R2 — …

## Context & Constraints

<optional: non-obvious facts, affected systems, dependencies, and constraints
needed to interpret the relevant R IDs>

## Design & Contracts

<optional: settled architecture, boundaries, data flow, and external contracts
that elaborate the relevant R IDs>

## Compatibility, Migration & Failure Behavior

<optional: compatibility, transition, edge-case, and failure details for the
relevant R IDs>

## Acceptance Criteria

- A1 (R1): <observable verification; include the exact command and working
  directory where one exists>
- A2 (R1, R2): …
```

Keep the sections in the template's order. Include each optional middle
section only when it has load-bearing content.

Outcome-level exclusions belong in `Objective & Scope`. Prohibitions within
the outcome are numbered requirements. Every in-scope obligation has one
stable `R<n>`; optional sections only elaborate those IDs. Every `R<n>` is
covered by an observable `A<n>`. Omit implementation plans, progress, review
records, and rejected alternatives unless an alternative defines the outcome
boundary or a numbered obligation.
