---
name: ai-attribution
description: Identify and record AI contributions to a change, distinguishing observed from reported or inferred, bound to evidence and confidence. Use when attributing generated or AI-assisted code to an agent, or deciding whether an AI claim is supported.
---

# Skill: AI Attribution

**Version:** 1.0.0

## Purpose

Identify and record AI contributions to a change, distinguishing what was observed from what was reported or merely inferred, and binding every claim to evidence and confidence.

## Scope

In scope: attributing a contribution (or part of one) to an AI agent, at a stated confidence, with a stated evidence basis.

Out of scope: judging code quality, deciding architecture, or measuring "engineering work" from line counts.

## Inputs

Evidence that an AI participated, from sources such as:

```text
AI agent logs
Agent-generated patches
Tool metadata
Commit metadata
Session records
IDE integrations
Explicit agent declarations
Human-provided records
```

## Preconditions

- A contribution exists (or is being created) to attribute.
- Evidence, if any, is captured as Evidence Records with a provenance class.

## Workflow

1. Gather AI-related evidence for the contribution.
2. Classify the AI contribution:
   - **Observed** — captured by a trusted system (e.g., IDE telemetry the project controls).
   - **Reported** — asserted by the agent or a human record.
   - **Inferred** — deduced from indirect signals.
   - **Unknown** — insufficient information.
3. Identify the agent generically (`agent_id`, `agent_name`, `provider`, `model`, `session`, `environment`) using only known fields.
4. Set `basis` to the strongest evidence class and `confidence` accordingly.
5. Write an Attribution Record (`contributor_type: AI_AGENT`) per [`schemas/attribution-record.md`](../schemas/attribution-record.md).
6. Set verification status via the Contribution Verification skill.

## Rules

- **Never treat inference as fact.** An `INFERRED` basis cannot exceed `LOW` confidence and is never `VERIFIED`.
- Do not infer a specific agent from code style ("this looks like Codex").
- Populate only known agent fields; the rest are `UNKNOWN`.
- An agent's self-claim is `REPORTED`, not `OBSERVED`.

## Evidence Requirements

- `confidence = HIGH` requires multiple corroborating OBSERVED items.
- `confidence = MEDIUM` requires one OBSERVED item or several agreeing REPORTED items.
- `confidence = LOW` for a single REPORTED item or weak signals.
- No evidence → `confidence = UNKNOWN`.

## Attribution Rules

- Distinguish Observed / Reported / Inferred / Unknown AI contribution explicitly in `basis` and notes.
- A change with an AI generation followed by human edits carries both an AI attribution and human attribution(s); do not blend them.

## Outputs

- An Attribution Record for the AI contribution, linked to evidence, with confidence and verification status.

## Verification

- Corroborate the agent session/patch (REPORTED) against Git or IDE evidence (OBSERVED).
- If corroboration is absent, cap confidence and mark `UNVERIFIED` or `PARTIALLY_VERIFIED`.

## Failure Conditions

- Only inferred signals exist → confidence `LOW`, basis `INFERRED`, never `VERIFIED`.
- Conflicting agent claims → `UNKNOWN` agent identity until resolved; record the conflict.

## Limitations

- Cannot prove which exact lines an agent authored from a session transcript alone.
- Absence of an AI record does not prove human authorship.

## Compatibility

- Works across Kiro, Codex, Claude Code, OpenCode, Cursor, Copilot, and custom or unknown agents. See [`adapters/`](../adapters/) for how each ecosystem can supply evidence.

## Examples

```text
ATTR-001
  contribution_id:     CONTRIBUTION-001
  contributor_type:    AI_AGENT
  agent_ref:           agent/claude-code
  contribution_kind:   CODE_GENERATION
  basis:               REPORTED
  confidence:          MEDIUM
  evidence:            [EVIDENCE-001, EVIDENCE-002]
  verification_status: PARTIALLY_VERIFIED
  notes:               Session (REPORTED) claims generation; commit (OBSERVED) corroborates
                       artifact and timing, not exact-line authorship.
```

Anti-pattern (do NOT do this):

```text
# WRONG: inferring the agent from style, with no evidence
ATTR-XXX
  contributor_type: AI_AGENT
  agent_ref:        agent/codex        # guessed from "the code looks like Codex"
  basis:            INFERRED
  confidence:       HIGH               # invalid: inferred basis cannot be HIGH/VERIFIED
```
