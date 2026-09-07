---
name: human-review
description: Track human review, approval, rejection, and validation activity as evidence-backed contributions. Use to capture the human oversight dimension of AI-assisted development.
---

# Skill: Human Review

**Version:** 1.0.0

## Purpose

Track human review, approval, rejection, and validation activity as evidence-backed contributions, capturing the human oversight dimension of AI-assisted development.

## Scope

In scope: recording review-related human actions (REVIEWED, APPROVED, REJECTED, VALIDATED) and their evidence.

Out of scope: general code review methodology or quality gates (external systems own those; CodeOrigin records only the provenance of the review act).

## Inputs

- PR review records, approvals, rejections, review comments.
- Human validation statements or CI sign-offs attributed to a human.

## Preconditions

- A contribution or change exists that was reviewed.

## Workflow

1. Identify the review action and the reviewer (`contributor_id`, repository-local).
2. Gather review evidence (PR approval, review thread, sign-off).
3. Set `contribution_kind = CODE_REVIEW`, `APPROVAL`, or `VALIDATION` as appropriate.
4. Record a Contribution Record and Attribution Record (`contributor_type: HUMAN`).
5. Set confidence/verification from evidence (PR approvals are typically `OBSERVED`).

## Rules

- Review is a first-class contribution even without code changes.
- Do not assume a review occurred without evidence.
- Prefer anonymous/local reviewer identifiers.

## Evidence Requirements

- Approvals/rejections should reference PR or review-system evidence.
- A verbal/unrecorded review is `REPORTED` at best.

## Attribution Rules

- A reviewer attribution is separate from the author/generator attribution on the same contribution.

## Outputs

- Contribution + Attribution Records for the human review activity.

## Verification

- PR-system approvals observed directly are `OBSERVED` and can support `VERIFIED`.
- Unrecorded reviews remain `UNVERIFIED`.

## Failure Conditions

- No review evidence → do not record a review.
- Ambiguous reviewer identity → `UNKNOWN`.

## Limitations

- The presence of an approval does not guarantee review depth or quality; it records the act, not its rigor.

## Compatibility

- Any PR/review system. Agent-, language-, framework-agnostic.

## Examples

```text
CONTRIBUTION-003
  contributor_type:  HUMAN
  contributor_id:    contrib/b.lead
  action:            APPROVED
  contribution_kind: APPROVAL
  artifact:          PR-42
  change_ref:        PR-42
  evidence:          [EVIDENCE-004]
  confidence:        HIGH
  verification_status: VERIFIED

ATTR-003
  contribution_id:   CONTRIBUTION-003
  contributor_type:  HUMAN
  contributor_id:    contrib/b.lead
  contribution_kind: APPROVAL
  basis:             OBSERVED
  confidence:        HIGH
  evidence:          [EVIDENCE-004]
  verification_status: VERIFIED
```
