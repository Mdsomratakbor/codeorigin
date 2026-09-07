# Schema: Contribution Record

**Version:** 1.0.0
**Status:** Canonical (stability-sensitive)

A **Contribution Record** is the central unit of CodeOrigin. It represents a single, meaningful act of contribution to an artifact — a generation, modification, review, approval, prompt, and so on. A contribution links an actor's action to an artifact and a change, and carries the attributions, evidence, and verification that support it.

One contribution may have **multiple attributions** (via Attribution Records) because a change is frequently collaborative. Contribution Records connect to Provenance Records to express how a contribution evolved over time.

---

## Fields

| Field | Required | Type | Description |
|---|---|---|---|
| `contribution_id` | yes | string | Unique identifier. Example: `CONTRIBUTION-001`. |
| `contributor_type` | yes | enum | Primary contributor type: `HUMAN` \| `AI_AGENT` \| `AUTOMATED_TOOL` \| `UNKNOWN`. |
| `contributor_id` | no | string | Repository-local or anonymous ID. `UNKNOWN` if not known. |
| `agent_ref` | no | string | If AI, reference to the agent identity (provenance-record `agent`). |
| `action` | yes | enum | What was done. See **Actions**. |
| `contribution_kind` | no | enum | Dimension of contribution (see attribution-record Contribution Kinds). |
| `artifact` | yes | string | The artifact affected. Example: `src/auth/token.ts`, `docs/README.md`, `PR-42`. |
| `change_ref` | no | string | The change this contribution belongs to. Example: commit SHA, PR ID. |
| `timestamp` | no | ISO-8601 | When the contribution occurred. `UNKNOWN` if not reliably known. |
| `source` | no | string | Where the contribution was recorded from. Example: `git`, `kiro-session`, `manual`. |
| `attributions` | no | string[] | Attribution Record IDs applying to this contribution. |
| `evidence` | no | string[] | Evidence IDs supporting the contribution. |
| `parent_contribution` | no | string | The contribution this one derives from (e.g., a modification of a generation). |
| `related_contributions` | no | string[] | Other contributions in the same change or chain. |
| `confidence` | yes | enum | `HIGH` \| `MEDIUM` \| `LOW` \| `UNKNOWN`. Overall confidence in this record. |
| `verification_status` | yes | enum | `VERIFIED` \| `PARTIALLY_VERIFIED` \| `UNVERIFIED` \| `CONTRADICTED` \| `UNKNOWN`. |
| `notes` | no | string | Caveats or reviewer notes. |

---

## Actions

```text
GENERATED
MODIFIED
DELETED
REVIEWED
APPROVED
REJECTED
EDITED
MERGED
VERIFIED
DIRECTED
PROMPTED
VALIDATED
```

A human may contribute via `DIRECTED`, `PROMPTED`, `REVIEWED`, `APPROVED`, or `VALIDATED` without writing a single line. This is a first-class contribution, not a lesser one.

---

## Collaboration Is First-Class

Do not force a collaborative change into a single `AI` or `HUMAN` bucket. A representative chain:

```text
Human PROMPTED / DIRECTED
        ↓
AI_AGENT GENERATED
        ↓
Human MODIFIED
        ↓
Human REVIEWED
        ↓
Human APPROVED
        ↓
MERGED
```

Each step is its own Contribution Record, linked by `parent_contribution` / `related_contributions`, and detailed by Provenance Records.

---

## Rules

1. `contribution_id` is stable and unique within the project. Identifier scheme is configurable.
2. The primary `contributor_type` reflects the actor of `action`. Additional contributors are expressed via `attributions`.
3. `confidence` and `verification_status` are mandatory and must be consistent with the evidence.
4. Never fabricate `contributor_id`, `timestamp`, or `change_ref`. Use `UNKNOWN`.
5. When a contribution derives from another (modification of a generation), set `parent_contribution`.

---

## Example

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
  parent_contribution: CONTRIBUTION-000   # human PROMPTED
  related_contributions: [CONTRIBUTION-002, CONTRIBUTION-003]
  confidence:          MEDIUM
  verification_status: PARTIALLY_VERIFIED
  notes:               Initial implementation; later modified by a human (CONTRIBUTION-002).

CONTRIBUTION-002
  contributor_type:    HUMAN
  contributor_id:      contrib/a.dev
  action:              MODIFIED
  contribution_kind:   CODE_MODIFICATION
  artifact:            src/auth/token.ts
  change_ref:          8b21d4e
  timestamp:           2026-09-05T15:10:00Z
  source:              git
  attributions:        [ATTR-002]
  evidence:            [EVIDENCE-003]
  parent_contribution: CONTRIBUTION-001
  confidence:          HIGH
  verification_status: VERIFIED
```
