---
name: contribution-reporting
description: Assemble contribution, attribution, provenance, evidence, and accounting data into clear, honest reports that preserve confidence, verification status, and the evidence-versus-inference distinction. Use to summarize provenance for a change, contributor, or period.
---

# Skill: Contribution Reporting

**Version:** 1.0.0

## Purpose

Assemble contribution, attribution, provenance, evidence, and accounting data into clear, honest reports that preserve confidence, verification status, and the evidence/inference distinction.

## Scope

In scope: producing human-readable reports (per change, per contributor, per period) from existing records.

Out of scope: computing new metrics (AI/Human Accounting) or auditing integrity (Provenance Audit).

## Inputs

- Contribution, Attribution, Provenance, Evidence Records.
- Accounting metrics (optional).

## Preconditions

- Records exist and are internally consistent.

## Workflow

1. Choose the report scope (a change, a contributor, a time window, a release).
2. Gather the relevant records.
3. Present each claim with its `confidence`, `verification_status`, and evidence basis.
4. Clearly label `UNKNOWN` and `UNVERIFIED` items rather than omitting them.
5. Include the measurement caveats for any metric.

## Rules

- Never present `INFERRED` or `REPORTED` claims as established fact.
- Always carry confidence and verification status into the report.
- Do not hide `UNKNOWN` or `CONTRADICTED` findings.

## Evidence Requirements

- Every reported claim links back to its evidence (by ID/locator).

## Attribution Rules

- Report collaborative contributions as multiple attributions, not a single blended figure.

## Outputs

- A Markdown (or other) report faithfully reflecting the records and their confidence.

## Verification

- A reader can trace any reported claim back to its evidence.

## Failure Conditions

- Source records inconsistent → report the inconsistency; do not paper over it.

## Limitations

- A report is only as good as the underlying records; it cannot add certainty that the evidence does not support.

## Compatibility

- Agent-, language-, framework-agnostic. Output format is flexible (Markdown-first).

## Examples

```text
## Contribution Report — src/auth/token.ts (PR-42)

Human contrib/a.dev  — PROMPT_DIRECTION   (MEDIUM, PARTIALLY_VERIFIED)  [EVIDENCE-001]
AI   Claude Code     — CODE_GENERATION    (MEDIUM, PARTIALLY_VERIFIED)  [EVIDENCE-001, EVIDENCE-002]
Human contrib/a.dev  — CODE_MODIFICATION  (HIGH, VERIFIED)              [EVIDENCE-003]
Human contrib/b.lead — APPROVAL           (HIGH, VERIFIED)              [EVIDENCE-004]

Accounting: AI-generated lines 124/142 (MEDIUM confidence; commit-level; lines != engineering work).
Unknown/unattributed: 0 changes.
```
