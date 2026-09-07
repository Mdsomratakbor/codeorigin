---
name: ai-human-accounting
description: Produce quantitative AI vs human contribution metrics while documenting method, source, assumptions, limitations, and confidence, and never conflating a count with intellectual contribution. Use to report contribution shares honestly.
---

# Skill: AI/Human Accounting

**Version:** 1.0.0

## Purpose

Produce quantitative measures of AI and human contribution (e.g., AI-attributed vs human-attributed changes) while explicitly documenting method, source data, assumptions, limitations, and confidence — and never conflating a count with intellectual contribution.

## Scope

In scope: computing metrics from existing records and stating their measurement basis.

Out of scope: judging value, productivity, or "who did more real work."

## Inputs

- Contribution, Attribution, and Evidence Records.
- A defined measurement method.

## Preconditions

- Records exist with attributions and (for line-based metrics) accessible diffs.

## Workflow

1. Choose the metric(s), e.g.:

   ```text
   AI-attributed changes
   Human-attributed changes
   AI-assisted changes
   Human-reviewed changes
   Unknown changes
   ```

2. Define and document the **measurement method** (what unit: changes, files, commits, lines; how counted).
3. Identify the **source data** and its provenance classes.
4. State **assumptions** and **limitations**.
5. Compute the metric.
6. Attach a **confidence** to the metric and caveats.
7. Report via the Contribution Reporting skill.

## Rules

- A line-based measure is a measure of lines, not of engineering contribution.
- Every metric must carry its method, source, assumptions, limitations, and confidence.
- Unknown contributions are counted as `UNKNOWN`, not distributed to AI or human.
- Do not fabricate percentages.

## Evidence Requirements

- Metrics must be reproducible from the cited source data.

## Attribution Rules

- Metrics aggregate attributions; they do not create or alter them.

## Outputs

- A metric set, each with method, source, assumptions, limitations, confidence.

## Verification

- A reviewer can recompute the metric from the same source data and method.

## Failure Conditions

- Missing source data → metric is `UNKNOWN` or omitted, with explanation.
- Method undefined → do not report a number.

## Limitations

- Counts (lines, files, commits) do not capture design, direction, or judgment.
- Attribution gaps bias metrics; report the `UNKNOWN` share explicitly.

## Compatibility

- Agent-, language-, framework-agnostic.

## Examples

```text
Metric:        AI-generated share of changed lines (this PR)
Method:        lines added/removed in commits attributed to AI_AGENT / total changed lines
Source data:   commits 3f9a1c2 (AI), 8b21d4e (human) via git diff
Assumptions:   line attribution follows commit-level attribution
Limitations:   commit-level; human edits within AI commits not separated
Confidence:    MEDIUM
Result:        AI-generated lines: 124/142 = 87% of changed lines

Explicit non-claim:
  "87% of changed lines" does NOT mean "AI contributed 87% of the engineering work."
```
