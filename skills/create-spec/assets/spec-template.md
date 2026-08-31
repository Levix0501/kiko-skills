# Spec template

The title and optional version metadata are non-authoritative. The
authoritative body has exactly the four sections below, in this order.

```markdown
# <Spec title>

## 1. Normative Requirements

### R1 — <name>

<A required result, externally visible behavior, quality or scope boundary,
or prohibited external effect whose meaning is independent of the internal
solution.>

## 2. Binding Design Decisions

### D1 (R1) — <name>

<An explicitly authorized task-specific internal choice or exclusion that
implementers may not replace.>

## 3. Observable Acceptance

### A1 (R1) — <name>

<An observable scenario, result, and pass condition derived entirely from the
referenced R and D items.>

### A2 (R1, D1) — <name>

<…>

## 4. Out of Scope

### O1 — <name>

<An unpromised result or capability whose explicit exclusion prevents a
reasonable scope misunderstanding.>
```

Use `None` as the entire content of section 2 or 4 when that section has no
items. Do not invent D or O items to fill it.

Use stable `R<n>`, `D<n>`, `A<n>`, and `O<n>` IDs; numbering implies no
implementation order. Each D heading cites the R items it constrains. Each A
heading cites at least one R and every D it observes. Every R and D has at
least one A. D authorization is verified during self-review, not recorded in
the spec.
