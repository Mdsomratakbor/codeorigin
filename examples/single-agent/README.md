# Example: Single Agent + Human Collaboration

**Environment:** TypeScript · Claude Code · GitHub · no specific SDD framework

A human directs an AI agent to implement token refresh. The agent generates the code, the human modifies it, and a second human approves the merge. This is a collaborative change — not "AI" and not "Human" alone.

## Narrative

```text
Human contrib/a.dev  PROMPTED / DIRECTED
        ↓
AI  Claude Code      GENERATED src/auth/token.ts
        ↓
Human contrib/a.dev  MODIFIED (18 lines)
        ↓
Human contrib/b.lead REVIEWED + APPROVED
        ↓
                     MERGED
```

## Agent Events

```text
EVENT-0001 SESSION_STARTED   AI_AGENT agent/claude-code S-2026-09-05-01 REPORTED
EVENT-0002 PROMPT_SUBMITTED  HUMAN    contrib/a.dev      parent=EVENT-0001 REPORTED
EVENT-0003 PATCH_GENERATED   AI_AGENT agent/claude-code  artifact=src/auth/token.ts REPORTED
EVENT-0004 COMMIT_CREATED    HUMAN    contrib/a.dev      artifact=3f9a1c2 OBSERVED
EVENT-0005 FILE_MODIFIED     HUMAN    contrib/a.dev      artifact=src/auth/token.ts OBSERVED
EVENT-0006 COMMIT_CREATED    HUMAN    contrib/a.dev      artifact=8b21d4e OBSERVED
EVENT-0007 APPROVAL          HUMAN    contrib/b.lead     artifact=PR-42 OBSERVED
```

## Evidence

```text
EVIDENCE-001 AGENT_SESSION REPORTED source=claude-code-session loc=session/2026-09-05T14-02-11Z.jsonl
             desc: transcript shows agent generated src/auth/token.ts on human prompt
EVIDENCE-002 GIT_COMMIT    OBSERVED source=git loc=3f9a1c2
             desc: commit adds src/auth/token.ts (+142 lines) 2026-09-05T14:38:20Z
EVIDENCE-003 GIT_COMMIT    OBSERVED source=git loc=8b21d4e
             desc: commit modifies 18 lines of src/auth/token.ts by contrib/a.dev
EVIDENCE-004 APPROVAL      OBSERVED source=github-pr loc=PR-42#approval
             desc: contrib/b.lead approved PR-42
```

## Contributions

```text
CONTRIBUTION-000 HUMAN contrib/a.dev  PROMPTED  kind=PROMPT_DIRECTION artifact=(session)
                 evidence=[EVIDENCE-001] confidence=MEDIUM verification=PARTIALLY_VERIFIED

CONTRIBUTION-001 AI_AGENT agent/claude-code GENERATED kind=CODE_GENERATION
                 artifact=src/auth/token.ts change=3f9a1c2
                 parent=CONTRIBUTION-000 attributions=[ATTR-001]
                 evidence=[EVIDENCE-001, EVIDENCE-002]
                 confidence=MEDIUM verification=PARTIALLY_VERIFIED

CONTRIBUTION-002 HUMAN contrib/a.dev MODIFIED kind=CODE_MODIFICATION
                 artifact=src/auth/token.ts change=8b21d4e
                 parent=CONTRIBUTION-001 attributions=[ATTR-002]
                 evidence=[EVIDENCE-003] confidence=HIGH verification=VERIFIED

CONTRIBUTION-003 HUMAN contrib/b.lead APPROVED kind=APPROVAL
                 artifact=PR-42 change=PR-42
                 attributions=[ATTR-003] evidence=[EVIDENCE-004]
                 confidence=HIGH verification=VERIFIED
```

## Attributions

```text
ATTR-001 -> CONTRIBUTION-001 AI_AGENT agent/claude-code CODE_GENERATION
         basis=REPORTED confidence=MEDIUM evidence=[EVIDENCE-001, EVIDENCE-002] PARTIALLY_VERIFIED
ATTR-002 -> CONTRIBUTION-002 HUMAN contrib/a.dev CODE_MODIFICATION
         basis=OBSERVED confidence=HIGH evidence=[EVIDENCE-003] VERIFIED
ATTR-003 -> CONTRIBUTION-003 HUMAN contrib/b.lead APPROVAL
         basis=OBSERVED confidence=HIGH evidence=[EVIDENCE-004] VERIFIED
```

## Report

```text
## Contribution Report — src/auth/token.ts (PR-42)

Human contrib/a.dev  — PROMPT_DIRECTION   (MEDIUM, PARTIALLY_VERIFIED)  [EVIDENCE-001]
AI   Claude Code     — CODE_GENERATION    (MEDIUM, PARTIALLY_VERIFIED)  [EVIDENCE-001, EVIDENCE-002]
Human contrib/a.dev  — CODE_MODIFICATION  (HIGH, VERIFIED)              [EVIDENCE-003]
Human contrib/b.lead — APPROVAL           (HIGH, VERIFIED)              [EVIDENCE-004]

Accounting (optional): AI-generated lines 124/142 (MEDIUM; commit-level; lines != engineering work).
Unknown/unattributed: none.
```

## Why the AI generation is only PARTIALLY_VERIFIED

The commit (OBSERVED) proves the artifact and timing, but exact-line authorship rests on the session transcript (REPORTED). Per the Contribution Verification skill, that is `PARTIALLY_VERIFIED` — not `VERIFIED`.
