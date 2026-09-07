# Skill: Contribution Verification

**Version:** 1.0.0

## Purpose

Independently assess whether a contribution claim is supported by evidence, and assign a verification status — never accepting a claim merely because an agent or human asserts it.

## Scope

In scope: evaluating claims against evidence and assigning `VERIFIED` / `PARTIALLY_VERIFIED` / `UNVERIFIED` / `CONTRADICTED` / `UNKNOWN`.

Out of scope: producing the evidence (Evidence Collection) or the attribution (AI/Human Attribution).

## Inputs

- A contribution claim (Contribution Record + Attribution Records).
- The Evidence Records referenced by the claim.

## Preconditions

- The claim identifies what is asserted (who, what action, which artifact).

## Workflow

1. State the claim precisely (e.g., "AI agent generated the initial implementation").
2. Enumerate the supporting evidence and its provenance classes.
3. Seek independent corroboration (e.g., session REPORTED + commit OBSERVED).
4. Check for contradicting evidence.
5. Assign verification status:

   ```text
   VERIFIED             - independently corroborated by OBSERVED evidence
   PARTIALLY_VERIFIED   - some aspects corroborated, others only reported
   UNVERIFIED           - claim stands only on reported/inferred signals
   CONTRADICTED         - evidence conflicts with the claim
   UNKNOWN              - insufficient evidence to assess
   ```

6. Record the rationale in notes; update the Contribution/Attribution records.

## Rules

- **Never mark VERIFIED merely because an agent claims it.** Self-claims are REPORTED.
- Inferred-only support can never be VERIFIED.
- Corroboration must be independent (distinct sources).
- CONTRADICTED is a valid, important outcome; do not suppress it.

## Evidence Requirements

- `VERIFIED` requires at least one OBSERVED item corroborating the specific claim.
- `PARTIALLY_VERIFIED` when the artifact/timing is observed but exact authorship is only reported.

## Attribution Rules

- Verification refines an attribution's `verification_status`; it does not create attributions.

## Outputs

- Updated `verification_status` on the affected records, with a documented rationale.

## Verification

- This skill IS the verification step. Its own output should be reproducible by another reviewer following the same evidence.

## Failure Conditions

- Evidence insufficient → `UNKNOWN` (not a guess).
- Only self-claims → `UNVERIFIED`.
- Conflicting evidence unresolved → `CONTRADICTED`, with both sides noted.

## Limitations

- Verification is bounded by evidence integrity; tampered evidence can mislead. Integrity classification limits trust.
- A `VERIFIED` artifact/timing does not verify the depth of intellectual contribution.

## Compatibility

- Agent-, language-, framework-agnostic.

## Examples

```text
Claim:   AI agent generated the initial implementation of src/auth/token.ts.
Evidence:
  EVIDENCE-001  AGENT_SESSION  REPORTED
  EVIDENCE-002  GIT_COMMIT     OBSERVED
Assessment:
  Artifact + timing corroborated by OBSERVED commit.
  Exact-line authorship only REPORTED by the session.
Result:  PARTIALLY_VERIFIED

Claim:   Human b.lead approved the change.
Evidence:
  EVIDENCE-004  APPROVAL  OBSERVED (PR approval record)
Result:  VERIFIED

Claim:   Agent X wrote 90% of the file.
Evidence: none (asserted in a chat message only)
Result:  UNVERIFIED
```
