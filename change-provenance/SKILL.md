# Skill: Change Provenance

**Version:** 1.0.0

## Purpose

Record the history of a contribution as it evolves — generation, modification, review, rejection, regeneration, approval, merge — so the final artifact is not the only source of provenance.

## Scope

In scope: building and preserving the lineage chain of a change across stages and contributors.

Out of scope: single-act recording (Contribution Tracking) and metrics (AI/Human Accounting).

## Inputs

- Contribution Records for each stage.
- Evidence linking stages (commits, PR events, session events).

## Preconditions

- Stages can be distinguished and ordered (or ordering is honestly `UNKNOWN`).

## Workflow

1. Create a provenance node per meaningful stage.
2. Link each node to its predecessor via `parent_contribution`; add siblings via `related_contributions`.
3. Retain rejected and regenerated stages — do not erase history.
4. In multi-agent flows, let a child node's agent differ from its parent's.
5. Set confidence/verification per node from evidence.

## Rules

- Never overwrite a parent node when adding a child.
- Rejection and regeneration are part of the history.
- Ordering that cannot be established is `UNKNOWN`, not guessed.

## Evidence Requirements

- Each transition should be supported by evidence (e.g., a follow-up commit shows modification).

## Attribution Rules

- Each stage carries its own attribution(s); the chain preserves who did what, when.

## Outputs

- A linked chain of Provenance Records per [`schemas/provenance-record.md`](../schemas/provenance-record.md) and [`protocols/provenance.md`](../protocols/provenance.md).

## Verification

- Confirm parent/related links resolve and are acyclic.
- Confirm each transition has supporting evidence.

## Failure Conditions

- Broken lineage link → repair or mark the gap explicitly.
- Cyclic lineage → invalid; resolve before recording.

## Limitations

- Only recorded stages appear; unrecorded intermediate work is invisible and must not be inferred.

## Compatibility

- Agent-, language-, framework-agnostic.

## Examples

```text
Lineage:
  CONTRIBUTION-000 (Human PROMPTED)
    -> CONTRIBUTION-001 (AI_AGENT GENERATED, agent/claude-code)
      -> CONTRIBUTION-002 (Human MODIFIED, contrib/a.dev)
        -> CONTRIBUTION-003 (Human APPROVED, contrib/b.lead)
          -> CONTRIBUTION-004 (MERGED)

Multi-agent handoff:
  Kiro GENERATED -> Codex MODIFIED -> Claude Code REVIEWED -> Human APPROVED
  (each node keeps its own agent identity)
```
