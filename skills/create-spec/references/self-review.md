# Spec self-review

Run this review on the complete first draft before writing it. After a requested
change, rerun every affected check, including shared terms and references.

Review with fresh eyes: read the draft as a reader who did not write it, so
memory of intent cannot fill gaps in the text. Check grounding, authorization,
and evidence against the actual record rather than memory.

The draft is ready only when every statement below is true:

1. **Structure:** after non-authoritative title or version metadata, the body
   contains exactly R, D, A, and O in template order. Metadata carries no
   obligation or approval status.
2. **Authority:** each R and O is grounded in settled input or an explicit user
   change; each D also has identifiable explicit authorization; each A is
   wholly derived from R and D. No authority conflict was silently resolved.
3. **R/D boundary:** every R remains meaningful if the internal solution
   changes. Every D is a non-replaceable internal choice. Mixed statements
   were split and no semantic unit appears in both.
4. **Obligation completeness:** R and D fully define which deliveries qualify
   without relying on A.
5. **References:** IDs are unique and stable; every D cites the R items it
   constrains; every A cites at least one R and every D it observes; every R
   and D has A coverage.
6. **Acceptance:** each A can decide its references within its scenario from
   observable input, state, or output. Removing A leaves the qualifying set
   unchanged, and changing only the proof method leaves the pass standard
   unchanged.
7. **Scope exclusions:** each O prevents a reasonable misunderstanding without
   weakening or contradicting R, D, or A. Empty D and O sections contain
   `None`.
8. **Terms:** every ambiguous contract term is defined at first use and keeps
   one meaning. A defines only observation-specific, set-preserving terms.
9. **Premises:** every empirical premise that influenced the contract or its
   readiness passed the evidence gate within the scope in which it was used.
   Facts and evidence are absent from the spec.
10. **Omission safety:** the spec contains only contract semantics. Material
    ambiguity was resolved rather than silently omitted.
11. **Feasibility:** missing feasibility proof was not treated as a blocker,
    and neither feasibility nor infeasibility was claimed without evidence.
    Any known material contradiction was resolved.
12. **Fresh context:** read with no conversation history, the spec and
    independently applicable authorities suffice to determine the required
    delivery, binding internal choices, conformance observations, and explicit
    scope exclusions.
