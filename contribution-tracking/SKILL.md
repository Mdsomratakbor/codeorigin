---
name: contribution-tracking
description: Record contributions to code as structured, evidence-backed records (who did what, to which artifact, when, on what basis). Use when logging an act of generation, modification, review, approval, or prompting during AI-assisted or human development.
---

# Skill: Contribution Tracking

**Version:** 1.0.0

## Purpose

Record contributions made during software development as structured, evidence-backed records, so a project can answer *who did what, to which artifact, when, and on what basis* — without fabricating anything.

## Scope

In scope: recording individual acts of contribution (generation, modification, review, approval, prompting, etc.) and linking them to artifacts, changes, evidence, and lineage.

Out of scope: requirements, specification, architecture, planning, general review/testing/security, and release management. CodeOrigin only tracks the contribution/provenance dimension of those activities.

## Inputs

- Development events (see [`protocols/agent-events.md`](../protocols/agent-events.md)).
- Evidence items (commits, diffs, PRs, sessions, human statements).
- Optional external metadata (SDD artifacts, CI events) treated as evidence, never as authority.

## Preconditions

- A stable identifier scheme for records (configurable; examples use `CONTRIBUTION-001`).
- At least one signal exists for the act being recorded. No signal, no record.

## Workflow

1. Identify the act: its `action` and `artifact`.
2. Determine the primary `contributor_type` (`HUMAN` / `AI_AGENT` / `AUTOMATED_TOOL` / `UNKNOWN`) and, if known, `contributor_id` / `agent_ref`.
3. Attach `change_ref` (commit/PR/patch) if a change exists.
4. Collect and link evidence (see Evidence Collection skill).
5. Add Attribution Records for collaborators; reference them in `attributions`.
6. Link lineage via `parent_contribution` / `related_contributions`.
7. Set `confidence` and `verification_status` from evidence.
8. Write the Contribution Record per [`schemas/contribution-record.md`](../schemas/contribution-record.md).

## Rules

- Use `UNKNOWN` for any field that cannot be established from evidence.
- A human contribution need not be code (DIRECTED, PROMPTED, REVIEWED, APPROVED, VALIDATED are first-class).
- Do not collapse collaborative work into a single actor.
- `confidence` and `verification_status` are mandatory.

## Evidence Requirements

- Each contribution should reference at least one Evidence Record when `confidence > UNKNOWN`.
- Evidence must carry its provenance class (OBSERVED / REPORTED / INFERRED / UNKNOWN).

## Attribution Rules

- Primary contributor reflects the actor of `action`; additional contributors go in `attributions`.
- Never infer an agent from code style, nor a human from the absence of an AI record.

## Outputs

- One or more Contribution Records (schema-conformant), linked to evidence and attributions.

## Verification

- Confirm each contribution's evidence supports its `confidence`.
- Confirm lineage links resolve to existing records.
- Set `verification_status` per [`protocols/contribution.md`](../protocols/contribution.md) and the Contribution Verification skill.

## Failure Conditions

- No signal exists → do not create a record.
- Contradictory evidence unresolved → `confidence = UNKNOWN`, note the contradiction.
- Identifier collision → resolve before writing.

## Limitations

- Tracks recorded acts only; unrecorded work is invisible and must not be inferred.
- Line/artifact counts are not intellectual contribution.

## Compatibility

- Agent-agnostic, language-agnostic, framework-agnostic. Works with any or no SDD framework and any Git provider.

## Examples

```text
CONTRIBUTION-001
  contributor_type:    AI_AGENT
  agent_ref:           agent/claude-code
  action:              GENERATED
  contribution_kind:   CODE_GENERATION
  artifact:            src/auth/token.ts
  change_ref:          3f9a1c2
  timestamp:           2026-09-05T14:02:11Z
  source:              claude-code-session
  attributions:        [ATTR-001]
  evidence:            [EVIDENCE-001, EVIDENCE-002]
  parent_contribution: CONTRIBUTION-000
  confidence:          MEDIUM
  verification_status: PARTIALLY_VERIFIED

CONTRIBUTION-002
  contributor_type:    HUMAN
  contributor_id:      contrib/a.dev
  action:              MODIFIED
  artifact:            src/auth/token.ts
  change_ref:          8b21d4e
  evidence:            [EVIDENCE-003]
  parent_contribution: CONTRIBUTION-001
  confidence:          HIGH
  verification_status: VERIFIED
```
