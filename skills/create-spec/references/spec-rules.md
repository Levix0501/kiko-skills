# Spec rules

These rules decide what each decisions entry may enter the spec as, and gate the
empirical facts used while forming it. To return an issue is to put it to the
user; do not draft a version for confirmation until it is resolved.

## Authority

R and O must be grounded in a decisions entry. D must be grounded in an entry
that binds the internal choice, which the implementer may not replace; an
entry whose answer leaves the choice open binds nothing.

A does not need separate authorization. It may be written by `create-spec` only
as an exact observable derivation of R and D.

Repository rules, organizational policy, and other higher authority apply
independently. Do not copy them into the spec merely for self-containment. If
these authorities and the settled input imply different contract results,
return the conflict instead of choosing between them.

## R: normative requirement

Ask:

1. If the statement were false, would delivery violate what the settled input
   requires?
2. Would its contract meaning stay the same if the internal solution changed?

Two yes answers admit R. If only the first answer is yes, separate the internal
choice and test it as D.

R covers required results, externally visible behavior, quality, scope
boundaries, and external effects the work must not cause. It does not describe
current implementation or turn advice, inference, or a default solution into
an obligation.

## D: binding design decision

Ask:

1. Does the statement choose or exclude a task-specific internal solution?
2. Does a decisions entry bind that choice?

Two yes answers admit D. Otherwise: omit a design clearly not meant to bind;
return the ambiguity when an entry supplies a design without settling whether
it binds; and return the choice as unresolved when the contract depends on it.
`create-spec` does not originate D from its own recommendation, inference, or
preferred default, and whole-document confirmation does not cure such an
insertion.

R and D are mutually exclusive at the semantic-unit level. Split a mixed
statement and test each part independently.

## A: observable acceptance

Ask:

1. Can observable input, state, or output decide the cited R and D within the
   described scenario?
2. If A is removed, is the set of qualifying deliveries unchanged?
3. If the observation method changes while the scenario meaning stays fixed,
   is the pass standard unchanged?

Three yes answers admit A. If the second answer is no, classify the new
obligation as R or D. If the third answer is no, remove the replaceable proof
procedure.

A may restate or instantiate semantics already defined by R and D. It cannot
first introduce a condition that changes which deliveries qualify. A proof
method explicitly authorized as binding belongs in R or D first; A then
observes it. If no observable A can cover an R or D, its conformance is not
decidable; return the item rather than inventing coverage.

## O: out of scope

Ask:

1. Can all R, D, and A remain fully satisfied after this result is excluded?
2. Would omitting the exclusion create a reasonable scope misunderstanding?

Two yes answers admit O. O states an unpromised result or capability. It does
not hold an unresolved choice, risk, factual uncertainty, implementation
tradeoff, or incomplete obligation.

`create-spec` does not originate O. If a reasonable misunderstanding has no
grounded exclusion, return the proposed exclusion; the user's explicit
acceptance grounds it.

## Contract terms

A contract-critical term that lacks one unambiguous ordinary meaning is
defined in the Glossary; a definition introduces no obligation. A may define
inline an observation-specific term that does not change the qualifying set.
Every use preserves the defined meaning.

## Non-contract content

Omit content whose role is to describe current state, advise an implementation,
or prescribe a replaceable execution procedure rather than define the delivery
contract. This includes project facts, plans, investigation records, progress,
and proof procedures. A project fact the contract relies on is recorded in
the facts file, not the spec. If non-contractual status is unclear and
omission could change user intent, return the ambiguity instead of deleting
it.

## Load-bearing premise evidence gate

An empirical premise is a claim about the world that can be true or false
independently of the contract; a normative choice is not one merely because
it is unimplemented or its feasibility is unknown.

Before a premise shapes the spec, ask whether the contract or its readiness
would differ if the premise were false. If not, it does not affect the spec.
If so, rely on it only with evidence that establishes it for the scope in
which it carries contract weight and supports no broader conclusion than the
one used. The evidence does not enter the spec; the premise, its source, and
the R/D it bears on are recorded in the facts file.

If a premise cannot be established:

- use premise-independent wording when all reasonable interpretations yield
  the same contract;
- return the uncertainty when interpretations change the contract; or
- let the user choose an obligation under explicit uncertainty without
  treating that choice as evidence.

## Contract completeness and feasibility

A contract is ready for confirmation when its obligations are complete, it has
no known material conflict, and conformance is decidable. Input that settles
no R gives nothing to confirm; return that instead of drafting.

`create-spec` need not prove that a successful implementation exists and,
without evidence, claims neither feasibility nor infeasibility. Reliable
evidence that no allowed solution can satisfy all obligations, or that
applicable authorities materially conflict, blocks confirmation until the
conflict is resolved.
