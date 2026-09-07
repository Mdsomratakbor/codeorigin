# Adapter: Agent Trace (interop)

**Status:** Optional interop adapter. Does not modify the canonical model.

[Agent Trace](https://agent-trace.com/) is an open, vendor-neutral specification for recording AI-generated code alongside human authorship in version-controlled codebases. This adapter explains how to **consume Agent Trace records as evidence** in CodeOrigin — turning an existing attribution format into an input for CodeOrigin's evidence, verification, and audit layer.

CodeOrigin does not replace Agent Trace and does not re-implement it. Where a project already emits Agent Trace, CodeOrigin ingests it and adds independent verification.

## Why interop rather than competition

Agent Trace and CodeOrigin share the same core contributor vocabulary. Agent Trace's four classifications map directly onto CodeOrigin contributor types:

| Agent Trace | CodeOrigin `contributor_type` |
|---|---|
| `human` | `HUMAN` |
| `ai` | `AI_AGENT` |
| `mixed` | collaborative: multiple linked contributions (AI + HUMAN) |
| `unknown` | `UNKNOWN` |

The value CodeOrigin adds is **not** a new attribution format. It is the evidence-class discipline (OBSERVED / REPORTED / INFERRED), confidence tied to evidence, and independent verification status on top of whatever Agent Trace asserts.

## Signal Mapping

| Agent Trace signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| An Agent Trace record exists in the repo | Evidence | `MANUAL_RECORD` / `IDE_EVENT` | OBSERVED (the record exists) |
| Its claim "this code is `ai`/`mixed`" | Attribution basis | — | REPORTED (it is an assertion, not proof) |
| Named agent/model in the record | Agent sub-model (`agent-provenance`) | — | REPORTED |
| Session/prompt/response captured | Agent Event | `AGENT_SESSION` / `PROMPT_SUBMITTED` | REPORTED |
| The commit the record annotates | Evidence | `GIT_COMMIT` | OBSERVED |

## Important: existence is observed, authorship is reported

An Agent Trace record's *presence* in version control is `OBSERVED` — you can see the file/trailer. But its *claim* about who authored the code is `REPORTED` — Agent Trace records the assertion at interaction time, which is stronger than post-hoc detection, but CodeOrigin still treats it as a reported claim to be corroborated (e.g., against the commit diff). This keeps CodeOrigin's `VERIFIED` status meaningful.

## Consuming Agent Trace

1. Read the Agent Trace record(s) for a change.
2. Create Evidence Records: the record itself (`MANUAL_RECORD`, OBSERVED existence) and any captured session (`AGENT_SESSION`, REPORTED).
3. Create Contribution + Attribution Records mapping the contributor type. For `mixed`, create separate AI and human contributions linked by lineage — do not collapse into one.
4. Run the Contribution Verification skill: corroborate the reported authorship with the `GIT_COMMIT` (OBSERVED). Set `PARTIALLY_VERIFIED` for artifact/timing, `VERIFIED` only with independent corroboration.
5. Optionally run Provenance Audit to flag Agent Trace claims that lack corroboration.

## Gaps

- Agent Trace `mixed` does not, by itself, say *which* lines are AI vs human; keep line-level accounting caveated (see AI/Human Accounting skill).
- A record can be added or edited after the fact; integrity is bounded by VCS/signing policy.

## Notes

The recommended relationship: **Agent Trace captures the claim at interaction time; CodeOrigin verifies and audits it.** If both are present, prefer Agent Trace as the source of the raw claim and use CodeOrigin for the evidence/verification/audit layer.
