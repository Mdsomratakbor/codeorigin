# Schema: Attribution Record

**Version:** 1.0.0
**Status:** Canonical (stability-sensitive)

An **Attribution Record** states *who* a contribution is attributed to, *at what confidence*, and *on what evidence*. Attribution is deliberately separated from the contribution itself so that a single contribution can carry multiple attributions (e.g., AI generation + human modification + human approval), and so attributions can be revised as evidence changes without rewriting the contribution.

An attribution is a **claim about a contributor**, always bound to evidence and confidence. An attribution with no evidence is `UNKNOWN` confidence.

---

## Fields

| Field | Required | Type | Description |
|---|---|---|---|
| `attribution_id` | yes | string | Unique identifier. Example: `ATTR-001`. |
| `contribution_id` | yes | string | The contribution this attribution applies to. |
| `contributor_type` | yes | enum | `HUMAN` \| `AI_AGENT` \| `AUTOMATED_TOOL` \| `UNKNOWN`. |
| `contributor_id` | no | string | Repository-local or anonymous ID of the contributor. `UNKNOWN` if not known. |
| `agent_ref` | no | string | If `contributor_type = AI_AGENT`, reference to an agent identity. |
| `contribution_kind` | no | enum | The dimension of contribution (see **Contribution Kinds**). |
| `basis` | yes | enum | `OBSERVED` \| `REPORTED` \| `INFERRED` \| `UNKNOWN`. The strongest evidence class supporting this attribution. |
| `confidence` | yes | enum | `HIGH` \| `MEDIUM` \| `LOW` \| `UNKNOWN`. Must be justified by `evidence`. |
| `evidence` | yes | string[] | Evidence IDs supporting this attribution. May be empty only when `confidence = UNKNOWN`. |
| `verification_status` | no | enum | `VERIFIED` \| `PARTIALLY_VERIFIED` \| `UNVERIFIED` \| `CONTRADICTED` \| `UNKNOWN`. |
| `notes` | no | string | Caveats, competing interpretations, or reviewer notes. |

---

## Contribution Kinds

An attribution may specify the dimension of the contribution. Contribution is not reducible to lines of code.

```text
CODE_GENERATION
CODE_MODIFICATION
CODE_DELETION
CODE_REVIEW
PROMPT_DIRECTION
ARCHITECTURE_INPUT
SPECIFICATION_INPUT
TESTING
VALIDATION
DOCUMENTATION
APPROVAL
INTEGRATION
```

---

## Confidence Must Track Evidence

Confidence is not a feeling; it is a function of evidence class and corroboration.

```text
HIGH     - multiple OBSERVED evidence items that corroborate each other
MEDIUM   - one OBSERVED item, or several REPORTED items that agree
LOW       - a single REPORTED item, or weak/indirect signals
UNKNOWN  - no evidence, or evidence is contradictory and unresolved
```

An attribution based solely on `INFERRED` signals cannot exceed `LOW` confidence and must never be `VERIFIED`.

---

## Rules

1. Never fabricate a contributor identity. `contributor_id = UNKNOWN` is valid.
2. Do not infer an AI agent from code style, nor infer a human because no AI record exists.
3. `confidence` must be justified by the listed `evidence`. If `evidence` is empty, `confidence` is `UNKNOWN`.
4. A contribution may have several attribution records; do not collapse a collaborative reality into a single `AI` or `Human` claim.
5. `verification_status = VERIFIED` requires independent corroboration per the verification protocol, not an agent's self-claim.

---

## Example

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
                       the artifact and timing but not authorship of the exact lines.

ATTR-002
  contribution_id:     CONTRIBUTION-001
  contributor_type:    HUMAN
  contributor_id:      contrib/a.dev
  contribution_kind:   CODE_MODIFICATION
  basis:               OBSERVED
  confidence:          HIGH
  evidence:            [EVIDENCE-003]
  verification_status: VERIFIED
  notes:               Follow-up commit by the human modified 18 of the generated lines.

ATTR-003
  contribution_id:     CONTRIBUTION-001
  contributor_type:    HUMAN
  contributor_id:      contrib/b.lead
  contribution_kind:   APPROVAL
  basis:               OBSERVED
  confidence:          HIGH
  evidence:            [EVIDENCE-004]
  verification_status: VERIFIED
```
