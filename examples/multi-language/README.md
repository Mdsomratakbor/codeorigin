# Example: Multi-Language, Multi-Environment

The same CodeOrigin model applied to three different projects. The language, SDD framework, agent, and Git provider all differ; the contribution/attribution/provenance/evidence/verification model is identical.

## Project A — .NET + Spec Kit + Codex

```text
Human contrib/x.dev  DIRECTED (Spec Kit spec SK-3)      kind=SPECIFICATION_INPUT
AI    Codex          GENERATED src/Payments/Refund.cs   kind=CODE_GENERATION  change=commit a1
Human contrib/x.dev  REVIEWED + APPROVED                kind=APPROVAL         change=PR-11

Evidence:
  E-A1 MANUAL_RECORD REPORTED  Spec Kit spec SK-3
  E-A2 AGENT_SESSION REPORTED  Codex session
  E-A3 GIT_COMMIT    OBSERVED  commit a1 adds Refund.cs
  E-A4 APPROVAL      OBSERVED  PR-11 approval

Codex generation -> PARTIALLY_VERIFIED (commit observed, exact authorship reported).
Approval -> VERIFIED.
```

## Project B — Python + OpenSpec + Kiro

```text
Human contrib/y.dev  DIRECTED (OpenSpec proposal OS-8)  kind=SPECIFICATION_INPUT
AI    Kiro           GENERATED app/reports/monthly.py   kind=CODE_GENERATION  change=commit b1
Human contrib/y.dev  MODIFIED  app/reports/monthly.py   kind=CODE_MODIFICATION change=commit b2

Evidence:
  E-B1 MANUAL_RECORD REPORTED  OpenSpec proposal OS-8
  E-B2 AGENT_SESSION REPORTED  Kiro session
  E-B3 GIT_COMMIT    OBSERVED  commit b1
  E-B4 GIT_COMMIT    OBSERVED  commit b2 (human modification)

Kiro generation -> PARTIALLY_VERIFIED. Human modification -> VERIFIED.
```

## Project C — Go + No SDD Framework + Unknown Agent

```text
Human contrib/z.dev  MODIFIED cmd/server/main.go        kind=CODE_MODIFICATION change=commit c1
AI    UNKNOWN         ??? (a chat message claims an AI wrote a helper, no session retained)

Evidence:
  E-C1 GIT_COMMIT    OBSERVED  commit c1 by contrib/z.dev
  E-C2 MANUAL_RECORD REPORTED  chat note: "an AI helped with the retry helper" (no agent id, no session)

Human modification -> VERIFIED (OBSERVED commit).
AI involvement -> agent=UNKNOWN, basis=REPORTED, confidence=LOW, verification=UNVERIFIED.
The AI is NOT guessed to be any specific agent; it stays UNKNOWN.
```

## What Stays Constant Across A, B, C

- Contributor types, actions, and contribution kinds.
- Evidence classification (OBSERVED / REPORTED / INFERRED / UNKNOWN).
- Confidence tracking evidence; verification requiring independent corroboration.
- Unknown staying UNKNOWN (Project C's AI is never fabricated into a named agent).
- Collaboration represented as multiple linked contributions.

## What Varies

- Language (.NET / Python / Go).
- SDD framework (Spec Kit / OpenSpec / none).
- Agent (Codex / Kiro / unknown).
- Git/PR provider.

This is the success criterion from the master prompt: one model, many environments.
