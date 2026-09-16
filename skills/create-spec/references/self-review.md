# Spec self-review

Run this review on the complete first draft before writing it. After a requested
change, rerun every affected check, including shared terms and references.

Review with fresh eyes: read the draft as a reader who did not write it, so
memory of intent cannot fill gaps in the text. Check grounding, authorization,
and evidence against `decisions.md` and `facts.md` rather than memory.

The draft is ready only when every statement below is true:

1. **Structure:** the body contains Goal, Glossary, R, D, A, and O in
   template order; an empty Glossary, D, or O section contains `None`. Title,
   version metadata, and Goal carry no obligation or approval status, and the
   spec cites no decisions entry.
2. **Authority:** each R and O is grounded in a decisions entry; each D in an
   entry that binds it; each A is wholly derived from R and D. No authority
   conflict was silently resolved.
3. **R/D boundary:** every R remains meaningful if the internal solution
   changes. Every D is a non-replaceable internal choice. Mixed statements
   were split and no semantic unit appears in both.
4. **References:** IDs are unique and stable; every D cites the R items it
   constrains; every A cites at least one R and every D it observes; every R
   and D has A coverage.
5. **Acceptance:** each A can decide its references within its scenario from
   observable input, state, or output. Removing A leaves the qualifying set
   unchanged, so R and D alone define it; changing only the proof method
   leaves the pass standard unchanged.
6. **Scope exclusions:** each O prevents a reasonable misunderstanding without
   weakening or contradicting R, D, or A.
7. **Terms:** every contract-critical term lacking one unambiguous ordinary
   meaning is defined in the Glossary and keeps one meaning; no definition
   introduces an obligation. A defines only observation-specific,
   set-preserving terms.
8. **Premises:** every empirical premise that influenced the contract or its
   readiness passed the evidence gate within the scope in which it was used,
   and is recorded in the facts file with its source and the R/D it bears on.
   Facts and evidence are absent from the spec.
9. **Readiness:** the spec contains only contract semantics, none of them
   observable only after a push or deployment, and material ambiguity was
   returned rather than silently omitted. No known material conflict remains,
   and neither feasibility nor infeasibility is claimed without evidence.
10. **Fresh context:** read with no conversation history and with the Goal
    removed, the spec and independently applicable authorities suffice to
    determine the required delivery, binding internal choices, conformance
    observations, and explicit scope exclusions.
