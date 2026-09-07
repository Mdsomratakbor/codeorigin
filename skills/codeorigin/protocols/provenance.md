# Protocol: Provenance

**Version:** 1.0.0

This protocol defines how the lineage of a contribution is recorded and preserved. Related schema: [`schemas/provenance-record.md`](../schemas/provenance-record.md).

## Purpose

Capture where a contribution came from and how it evolved — the parent/related chain from generation through modification, review, and approval — so the final artifact is never the only source of truth.

## Procedure

1. **Create a provenance node** for each meaningful stage of a change.
2. **Record the actor.** Set `contributor` and `contributor_type`. If AI, populate the generic **agent sub-model** with only the fields actually known (`agent_id`, `agent_name`, `agent_version`, `provider`, `model`, `session`, `environment`); leave the rest `UNKNOWN`.
3. **Attach artifact and change.** Set `artifact` and, when present, `change`.
4. **Link evidence** via the evidence protocol.
5. **Preserve lineage.** Set `parent_contribution` to the node this derives from; add siblings to `related_contributions`. Never overwrite a parent when adding a child.
6. **Set confidence and verification** from evidence.

## Lineage Example

```text
Generated -> Modified -> Reviewed -> Rejected -> Regenerated -> Modified -> Approved -> Merged
```

Every stage is its own node. Rejection and regeneration are part of the history and must be retained, not erased.

## Multi-Agent and Agent Replacement

In multi-agent flows, a child node's agent may differ from its parent's (e.g., Kiro → Codex → Claude Code → Human). Each node keeps its own agent identity so the handoff chain stays distinguishable. Because the agent sub-model is generic, replacing one agent with another does not change the provenance model — only the values in specific nodes differ.

## Rules

1. Populate only known agent fields; the rest are `UNKNOWN`.
2. Preserve lineage; do not collapse or overwrite history.
3. `confidence` and `verification_status` are mandatory and evidence-justified.
4. Never fabricate agent identity, session, timestamp, or change.
