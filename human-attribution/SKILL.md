# Skill: Human Attribution

**Version:** 1.0.0

## Purpose

Record human participation in a change — including participation that does not involve writing code — as evidence-backed attributions.

## Scope

In scope: attributing contributions to humans across the full range of actions, including direction, prompting, review, approval, modification, and validation.

Out of scope: performance evaluation, identity management, or measuring human worth by line counts.

## Inputs

- Git author/committer metadata.
- Pull request reviews, approvals, comments.
- IDE edit events.
- Human-provided records (e.g., "I directed the agent to do X").
- Session transcripts showing human prompts/direction.

## Preconditions

- A contribution exists (or is being created) to attribute.
- Evidence is captured as Evidence Records with a provenance class.

## Workflow

1. Gather human-related evidence for the contribution.
2. Determine the human action:

   ```text
   Created  Modified  Reviewed  Approved  Rejected
   Edited   Merged    Verified  Directed  Prompted  Validated
   ```

3. Determine the `contribution_kind` (e.g., PROMPT_DIRECTION, CODE_MODIFICATION, CODE_REVIEW, APPROVAL, VALIDATION).
4. Use a repository-local or anonymous `contributor_id`; avoid unnecessary personal data.
5. Set `basis` and `confidence` from evidence.
6. Write an Attribution Record (`contributor_type: HUMAN`) per [`schemas/attribution-record.md`](../schemas/attribution-record.md).

## Rules

- A human may contribute without writing code (providing requirements, directing an agent, reviewing, modifying, approving).
- Do not infer human authorship merely because no AI record exists.
- Prefer anonymous/local identifiers over personal information (see [`SECURITY.md`](../SECURITY.md)).

## Evidence Requirements

- Approvals/reviews should reference PR or review evidence.
- Direction/prompting should reference session or human-record evidence (typically `REPORTED`).
- No evidence → `confidence = UNKNOWN`.

## Attribution Rules

- Non-code contributions (direction, review, approval) are first-class and recorded with their own `contribution_kind`.
- Multiple humans may attribute to one contribution (e.g., modifier + approver).

## Outputs

- Attribution Record(s) for human contributors, linked to evidence, with confidence and verification status.

## Verification

- Corroborate self-reported direction against a session transcript or PR history where possible.
- Approvals observed in a PR system are `OBSERVED` and can support `VERIFIED`.

## Failure Conditions

- Only a self-report exists with no corroboration → `LOW`/`MEDIUM` confidence, `UNVERIFIED`.
- Author metadata is spoofable; if signing/integrity is unknown, do not treat it as `VERIFIED` on its own.

## Limitations

- Git author metadata can be set arbitrarily; treat integrity per repository policy.
- The depth of a human's intellectual contribution is not captured by counts.

## Compatibility

- Agent-, language-, and framework-agnostic. Works with any Git/PR provider.

## Examples

```text
ATTR-002
  contribution_id:     CONTRIBUTION-001
  contributor_type:    HUMAN
  contributor_id:      contrib/a.dev
  contribution_kind:   CODE_MODIFICATION
  basis:               OBSERVED
  confidence:          HIGH
  evidence:            [EVIDENCE-003]
  verification_status: VERIFIED
  notes:               Follow-up commit modified 18 of the generated lines.

ATTR-003
  contribution_id:     CONTRIBUTION-001
  contributor_type:    HUMAN
  contributor_id:      contrib/b.lead
  contribution_kind:   APPROVAL
  basis:               OBSERVED
  confidence:          HIGH
  evidence:            [EVIDENCE-004]
  verification_status: VERIFIED

# Non-code human contribution: direction only
ATTR-000
  contribution_id:     CONTRIBUTION-000
  contributor_type:    HUMAN
  contributor_id:      contrib/a.dev
  contribution_kind:   PROMPT_DIRECTION
  basis:               REPORTED
  confidence:          MEDIUM
  evidence:            [EVIDENCE-001]
  verification_status: PARTIALLY_VERIFIED
```
