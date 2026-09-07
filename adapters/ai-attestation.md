# Adapter: ai-attestation (interop)

**Status:** Optional interop adapter. Does not modify the canonical model.

[ai-attestation](https://github.com/Korext/ai-attestation) is an open standard (CC0) for recording that code was AI-generated, with detection support across many AI coding tools. It commonly surfaces as Git commit trailers (for example `Generated-By:` and `Co-authored-by:` for bots) and/or attestation records. This adapter explains how to **consume ai-attestation signals as evidence** in CodeOrigin.

CodeOrigin does not re-implement ai-attestation or its detectors. It ingests the attestations and adds the evidence-class, confidence, verification, and audit layer.

## Signal Mapping

| ai-attestation signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| `Generated-By:` / attestation trailer on a commit | Evidence | `GIT_COMMIT` (trailer) | OBSERVED (trailer exists) / REPORTED (its authorship claim) |
| `Co-authored-by:` bot/agent line | Agent sub-model (`agent-provenance`) | — | REPORTED |
| Detector output ("tool X likely generated this") | Evidence | `MANUAL_RECORD` | INFERRED |
| Attestation record file | Evidence | `MANUAL_RECORD` | OBSERVED (exists) / REPORTED (claim) |
| The annotated commit/diff | Evidence | `GIT_COMMIT` / `GIT_DIFF` | OBSERVED |

## Two very different signal strengths

ai-attestation covers both *declared* attestations and *detector* estimates. CodeOrigin must keep these apart:

- A **declared trailer written at commit time** is a `REPORTED` claim (with `OBSERVED` existence in Git).
- A **post-hoc detector probability** is `INFERRED` and can never, alone, reach `VERIFIED` — it estimates origin but cannot recover the prompt, model version, or review status.

Collapsing these would violate CodeOrigin's core discipline. The adapter maps each to its honest class.

## Consuming ai-attestation

1. Parse trailers/attestation records for the change.
2. Create Evidence Records with the correct class: declared trailer → REPORTED (OBSERVED existence); detector output → INFERRED.
3. Map any named agent into the generic agent sub-model (`agent-provenance`) — populate only known fields, rest `UNKNOWN`. Do not infer a specific tool from detector guesses beyond `LOW` confidence.
4. Create AI (and, for reviewed code, human) Contribution + Attribution Records.
5. Verify (Contribution Verification): corroborate declared authorship against the commit; keep detector-only claims `UNVERIFIED`.
6. Audit (Provenance Audit): flag any `VERIFIED` claim resting only on a trailer or detector.

## Gaps

- Detector output is probabilistic; treat as `INFERRED`, `LOW` confidence, never `VERIFIED` on its own.
- A trailer can be added, copied, or omitted; integrity is bounded by VCS/signing policy.
- Tool identity from a detector is a guess, not a fact — respect the "do not infer the agent from style/heuristics" rule.

## Notes

Recommended relationship: **ai-attestation supplies the declared/detected signal; CodeOrigin classifies it (reported vs inferred), corroborates it, and produces an auditable verification status.**
