# Example: Multi-Agent Handoff

**Environment:** Python · Kiro → Codex → Claude Code → Human · GitLab · OpenSpec

One change passes through several agents before a human approves it. The provenance model preserves the handoff chain, and each node keeps its own agent identity. This also demonstrates **agent replaceability**: swapping any agent changes only the values in a node, not the model.

## Narrative

```text
Human contrib/a.dev   DIRECTED (via OpenSpec proposal)
        ↓
AI  Kiro              GENERATED src/pipeline/ingest.py (scaffold)
        ↓
AI  Codex             MODIFIED (added retry logic)
        ↓
AI  Claude Code       REVIEWED (suggested fixes) — one REJECTED
        ↓
AI  Codex             REGENERATED the rejected section
        ↓
Human contrib/a.dev   MODIFIED + APPROVED
```

## Agent Events (partial stream — not every agent emits every event)

```text
EVENT-1001 PROMPT_SUBMITTED  HUMAN    contrib/a.dev   REPORTED (references OpenSpec proposal OS-17)
EVENT-1002 PATCH_GENERATED   AI_AGENT agent/kiro      artifact=src/pipeline/ingest.py REPORTED
EVENT-1003 COMMIT_CREATED    HUMAN    contrib/a.dev   artifact=aa11bb2 OBSERVED
EVENT-1004 PATCH_GENERATED   AI_AGENT agent/codex     artifact=src/pipeline/ingest.py REPORTED
EVENT-1005 COMMIT_CREATED    HUMAN    contrib/a.dev   artifact=cc33dd4 OBSERVED
EVENT-1006 REVIEW_COMPLETED  AI_AGENT agent/claude-code artifact=MR-9 REPORTED
EVENT-1007 REJECTION         AI_AGENT agent/claude-code artifact=src/pipeline/ingest.py REPORTED
EVENT-1008 PATCH_GENERATED   AI_AGENT agent/codex     artifact=src/pipeline/ingest.py REPORTED
EVENT-1009 COMMIT_CREATED    HUMAN    contrib/a.dev   artifact=ee55ff6 OBSERVED
EVENT-1010 APPROVAL          HUMAN    contrib/a.dev   artifact=MR-9 OBSERVED
```

## Evidence

```text
EVIDENCE-101 MANUAL_RECORD  REPORTED source=openspec loc=OS-17
             desc: OpenSpec proposal OS-17 describes the ingest pipeline change (SPECIFICATION_INPUT)
EVIDENCE-102 AGENT_SESSION  REPORTED source=kiro-session
             desc: Kiro session shows scaffold generation of src/pipeline/ingest.py
EVIDENCE-103 GIT_COMMIT     OBSERVED source=git loc=aa11bb2 (scaffold)
EVIDENCE-104 AGENT_SESSION  REPORTED source=codex-session (retry logic)
EVIDENCE-105 GIT_COMMIT     OBSERVED source=git loc=cc33dd4 (retry logic)
EVIDENCE-106 AGENT_SESSION  REPORTED source=claude-code-session (review + rejection)
EVIDENCE-107 GIT_COMMIT     OBSERVED source=git loc=ee55ff6 (regenerated section)
EVIDENCE-108 APPROVAL       OBSERVED source=gitlab-mr loc=MR-9#approval (contrib/a.dev)
```

## Contributions (lineage preserved, including the rejection)

```text
CONTRIBUTION-100 HUMAN contrib/a.dev  DIRECTED  kind=SPECIFICATION_INPUT
                 evidence=[EVIDENCE-101] confidence=MEDIUM verification=PARTIALLY_VERIFIED

CONTRIBUTION-101 AI_AGENT agent/kiro   GENERATED kind=CODE_GENERATION
                 artifact=src/pipeline/ingest.py change=aa11bb2 parent=CONTRIBUTION-100
                 evidence=[EVIDENCE-102, EVIDENCE-103] confidence=MEDIUM verification=PARTIALLY_VERIFIED

CONTRIBUTION-102 AI_AGENT agent/codex  MODIFIED  kind=CODE_MODIFICATION
                 artifact=src/pipeline/ingest.py change=cc33dd4 parent=CONTRIBUTION-101
                 evidence=[EVIDENCE-104, EVIDENCE-105] confidence=MEDIUM verification=PARTIALLY_VERIFIED

CONTRIBUTION-103 AI_AGENT agent/claude-code REVIEWED kind=CODE_REVIEW
                 artifact=MR-9 parent=CONTRIBUTION-102
                 evidence=[EVIDENCE-106] confidence=LOW verification=UNVERIFIED

CONTRIBUTION-104 AI_AGENT agent/claude-code REJECTED kind=CODE_REVIEW
                 artifact=src/pipeline/ingest.py parent=CONTRIBUTION-103
                 evidence=[EVIDENCE-106] confidence=LOW verification=UNVERIFIED

CONTRIBUTION-105 AI_AGENT agent/codex  GENERATED kind=CODE_GENERATION (regeneration)
                 artifact=src/pipeline/ingest.py change=ee55ff6 parent=CONTRIBUTION-104
                 evidence=[EVIDENCE-107] confidence=MEDIUM verification=PARTIALLY_VERIFIED

CONTRIBUTION-106 HUMAN contrib/a.dev  APPROVED  kind=APPROVAL
                 artifact=MR-9 change=MR-9 parent=CONTRIBUTION-105
                 evidence=[EVIDENCE-108] confidence=HIGH verification=VERIFIED
```

## Key Points

- **Handoff preserved:** three different agents (`agent/kiro`, `agent/codex`, `agent/claude-code`) each keep distinct identities across the chain.
- **Rejection retained:** CONTRIBUTION-104 (a rejected review) stays in the lineage; it is not erased by the regeneration in CONTRIBUTION-105.
- **Agent-reported vs observed:** all agent generations are `REPORTED` and become `PARTIALLY_VERIFIED` only where an `OBSERVED` commit corroborates the artifact.
- **Agent replaceability:** replacing `agent/codex` with a custom agent would change only the `agent` values in nodes 102/105 — the schema, links, and rules are unchanged.
- **SDD independence:** OpenSpec proposal OS-17 enters as `SPECIFICATION_INPUT` evidence; it does not establish code authorship, and the example would still work with no SDD framework at all.
