# Schema: Provenance Record

**Version:** 1.0.0
**Status:** Canonical (stability-sensitive)

A **Provenance Record** is the canonical model of *where a contribution came from and how it evolved*. It ties together contributor, agent, action, artifact, change, evidence, and lineage into one auditable unit. Where a Contribution Record captures a single act, provenance emphasizes **lineage** — the parent/related chain that shows a change moving from generation through modification, review, and approval.

This is the most stability-sensitive schema in CodeOrigin. Downstream skills (traceability, verification, audit, accounting) depend on its shape.

---

## Canonical Fields

| Field | Required | Type | Description |
|---|---|---|---|
| `provenance_id` | yes | string | Unique identifier. Example: `PROV-001`. |
| `contribution_id` | no | string | The contribution this provenance describes, if modeled separately. |
| `contributor` | yes | string | Contributor identifier or `UNKNOWN`. |
| `contributor_type` | yes | enum | `HUMAN` \| `AI_AGENT` \| `AUTOMATED_TOOL` \| `UNKNOWN`. |
| `agent` | no | object | Agent identity (see **Agent Sub-Model**). Present when `contributor_type = AI_AGENT` and known. |
| `action` | yes | enum | See Contribution Record Actions. |
| `artifact` | yes | string | Artifact affected. |
| `change` | no | string | Change reference (commit, PR, patch ID). |
| `timestamp` | no | ISO-8601 | When it occurred. `UNKNOWN` if not reliably known. |
| `source` | yes | string | Where the provenance was recorded from. |
| `evidence` | no | string[] | Evidence IDs supporting this provenance. |
| `parent_contribution` | no | string | The contribution/provenance this derives from. |
| `related_contributions` | no | string[] | Sibling contributions in the same change or chain. |
| `confidence` | yes | enum | `HIGH` \| `MEDIUM` \| `LOW` \| `UNKNOWN`. |
| `verification_status` | yes | enum | `VERIFIED` \| `PARTIALLY_VERIFIED` \| `UNVERIFIED` \| `CONTRADICTED` \| `UNKNOWN`. |
| `notes` | no | string | Caveats or reviewer notes. |

---

## Agent Sub-Model

An agent is represented **generically**. Do not hard-code a fixed set of agents. Populate only fields that are actually known; leave the rest `UNKNOWN`.

| Field | Description |
|---|---|
| `agent_id` | Stable local reference. Example: `agent/claude-code`. |
| `agent_name` | Display name. Example: `Claude Code`. |
| `agent_version` | Version string if known. |
| `provider` | Vendor/provider if known. |
| `model` | Model identifier if known. |
| `session` | Session ID if known. |
| `environment` | IDE/CLI/CI environment if known. |

```text
agent:
  agent_id:      agent/claude-code
  agent_name:    Claude Code
  agent_version: UNKNOWN
  provider:      Anthropic
  model:         UNKNOWN
  session:       S-2026-09-05-01
  environment:   CLI
```

Unknown fields remain `UNKNOWN`. A project must be able to swap one agent for another (e.g., Kiro → Codex) without changing this model.

---

## Lineage

Provenance preserves the evolution of a change. The final artifact is **not** the only source of provenance.

```text
Generated
   ↓
Modified
   ↓
Reviewed
   ↓
Rejected
   ↓
Regenerated
   ↓
Modified
   ↓
Approved
   ↓
Merged
```

Each stage is a provenance/contribution node linked via `parent_contribution` and `related_contributions`. In multi-agent flows, a node's agent may differ from its parent's agent (e.g., Kiro → Codex → Claude Code → Human); the chain must preserve and distinguish each agent.

---

## Rules

1. Populate only known agent fields; everything else is `UNKNOWN`.
2. Preserve lineage; do not overwrite a parent node when a child is created.
3. `confidence` and `verification_status` are mandatory and evidence-justified.
4. Distinguish OBSERVED / REPORTED / INFERRED evidence in the linked evidence records.
5. Never fabricate agent identity, session, timestamp, or change.

---

## Example

```text
PROV-001
  contribution_id:  CONTRIBUTION-001
  contributor:      agent/claude-code
  contributor_type: AI_AGENT
  agent:
    agent_id:      agent/claude-code
    agent_name:    Claude Code
    provider:      Anthropic
    session:       S-2026-09-05-01
    environment:   CLI
  action:           GENERATED
  artifact:         src/auth/token.ts
  change:           3f9a1c2
  timestamp:        2026-09-05T14:02:11Z
  source:           claude-code-session
  evidence:         [EVIDENCE-001, EVIDENCE-002]
  parent_contribution: CONTRIBUTION-000
  related_contributions: [CONTRIBUTION-002]
  confidence:       MEDIUM
  verification_status: PARTIALLY_VERIFIED
```
