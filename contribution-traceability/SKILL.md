# Skill: Contribution Traceability

**Version:** 1.0.0

## Purpose

Provide end-to-end traceability linking contributor → action → artifact → change → evidence → verification, so any contribution can be followed from claim to supporting proof.

## Scope

In scope: establishing and validating the trace links across existing records.

Out of scope: producing evidence or metrics.

## Inputs

- Contribution, Attribution, Provenance, and Evidence Records.

## Preconditions

- Records exist and use a consistent identifier scheme.

## Workflow

1. For a target contribution, assemble the trace:

   ```text
   Contributor -> Action -> Artifact -> Change -> Evidence -> Verification
   ```

2. Resolve each link to an existing record.
3. Flag any missing or dangling link.
4. Confirm the verification status is consistent with the evidence at the end of the trace.

## Rules

- Every link in a trace must resolve to a real record.
- A missing link is reported as a gap, never fabricated.
- Traceability does not upgrade confidence; it exposes what supports a claim.

## Evidence Requirements

- The Evidence step must reference concrete Evidence Records with locators.

## Attribution Rules

- A contribution with multiple attributions yields multiple traces (one per contributor/kind).

## Outputs

- A resolved trace (or a gap report) for the target contribution.

## Verification

- All links resolve, no cycles, verification status consistent with evidence.

## Failure Conditions

- Dangling reference → gap report; do not invent the missing record.
- Cycle detected → invalid trace.

## Limitations

- Traceability reflects only recorded data; it cannot reveal untracked contributions.

## Compatibility

- Agent-, language-, framework-agnostic.

## Examples

```text
CONTRIBUTION-001
  Contributor:   AI_AGENT (agent/claude-code)
  Action:        GENERATED
  Artifact:      src/auth/token.ts
  Change:        COMMIT 3f9a1c2
  Evidence:      EVIDENCE-001 (REPORTED session), EVIDENCE-002 (OBSERVED commit)
  Verification:  PARTIALLY_VERIFIED

Gap report example:
  CONTRIBUTION-009 -> Evidence: [EVIDENCE-020]  (EVIDENCE-020 not found -> GAP)
```
