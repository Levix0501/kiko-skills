# Spec self-review

Read the spec as a fresh implementation context with no conversation history.

Check:

1. **Completeness:** nothing required for implementation exists only in the
   conversation.
2. **Facts:** load-bearing claims and verification prerequisites are supported
   by the workspace or an authoritative source.
3. **Authority:** the sections agree on one coherent outcome; every in-scope
   obligation or prohibition has one unique `R<n>`, and optional sections add
   no hidden obligation.
4. **Behavior:** requirements and supporting sections are observable and settle
   applicable contracts, compatibility, migration, edge cases, and failure
   behavior.
5. **Acceptance:** every requirement is covered by an `A<n>` that can
   distinguish a correct result using real commands, environments, accounts,
   devices, permissions, or other evidence where required.
6. **No process content:** there are no tasks, phases, predicted file changes,
   progress, TODO items, review logs, or build status.

Fix problems before user review. After a change, recheck its R/A and any shared
contract it affects.
