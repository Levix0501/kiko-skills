# Domain-neutral semantic cases

These are model-level regression cases, not shell assertions. Evaluate them by
giving each input to a fresh `create-spec` context and checking the result.
`run-tests` validates only the fixture and resource structure.

| Input or change | Expected result |
|---|---|
| Delivery remains acceptable when a statement is false | Do not write R |
| One statement combines an external obligation and an internal solution | Split it; test the D separately |
| An internal choice lacks explicit authorization | Omit D, or return an unresolved choice if the contract depends on it |
| The user explicitly adds an internal choice during review | Write D and recheck related A |
| The author recommends a preferred solution, then seeks whole-document confirmation | Do not write D |
| Removing A changes which deliveries qualify | Move the added obligation through R/D classification |
| A contains a replaceable proof procedure | Remove the procedure and retain stable observable semantics |
| A first uses an ambiguous observation-only term | Define it in A without changing the qualifying set |
| O conflicts with R, D, or A | Reject the draft |
| Content is clearly factual, procedural, or transient | Omit it |
| Omission might discard a user obligation | Return the ambiguity |
| An unsupported premise would change contract wording | Use premise-independent wording or return the uncertainty |
| R lacks feasibility proof and reliable counterevidence | Allow the contract without claiming proven feasibility |
| Reliable evidence rules out every allowed solution | Return the conflict |
| Applicable authorities imply different contract results | Return the conflict |
| R or D lacks A coverage | Reject the draft |
| A user change is written but the current file is unconfirmed | Do not commit |
| D or O is empty | Write `None` rather than inventing content |
| A new-domain input has an existing semantic role | Apply the same R/D/A/O tests |
| An empirical premise passes the evidence gate | Keep it out of the spec; record it in the notes file with source and bearing |
| A fact is omitted as non-contract content | Record it in the notes file |
