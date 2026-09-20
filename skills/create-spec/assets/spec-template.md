# Spec template

The title, optional version metadata, and Goal are non-authoritative. The
authoritative body is Glossary, Normative Requirements, Binding Design
Decisions, Observable Acceptance, and Out of Scope, in this order.

```markdown
# <Spec title>

## Goal

<Who, what changes, why, concisely. It creates no obligation: where it and
the body differ, the body governs; where a requirement is ambiguous, read it
consistently with the Goal.>

## Glossary

<Term: definition for each contract-critical term that lacks one
unambiguous ordinary meaning. A definition introduces no obligation.>

## Normative Requirements

### R1 — <name>

<A required result, externally visible behavior, quality or scope boundary,
or prohibited external effect whose meaning is independent of the internal
solution.>

## Binding Design Decisions

### D1 (R1) — <name>

<An explicitly authorized task-specific internal choice or exclusion that
implementers may not replace.>

## Observable Acceptance

### A1 (R1) — <name>

<An observable scenario, result, and pass condition derived entirely from the
referenced R and D items.>

### A2 (R1, D1) — <name>

<…>

## Out of Scope

### O1 — <name>

<An unpromised result or capability whose explicit exclusion prevents a
reasonable scope misunderstanding.>
```

Use `None` as the entire content of Glossary, Binding Design Decisions, or
Out of Scope when that section has no items. Do not invent items to fill
them.

Use stable `R<n>`, `D<n>`, `A<n>`, and `O<n>` IDs. Each D heading cites the R
items it constrains. Each A heading cites at least one R and every D it
observes. Every R and D has at least one A. The spec cites no decisions
entry: the implementer and reviewer read it without `decisions.md`.
