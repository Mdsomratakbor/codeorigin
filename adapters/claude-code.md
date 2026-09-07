# Adapter: Claude Code

**Status:** Optional. Does not modify the canonical model.

Maps signals from Claude Code (CLI coding agent) onto CodeOrigin schemas.

## Agent Identity

```text
agent_id:     agent/claude-code
agent_name:   Claude Code
provider:     Anthropic
model:        model id if surfaced, else UNKNOWN
session:      session id if available
environment:  CLI
```

## Signal Mapping

| Claude Code signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Session start | Agent Event | `SESSION_STARTED` | REPORTED |
| Human prompt | Agent Event | `PROMPT_SUBMITTED` | REPORTED |
| Response / tool use | Agent Event | `RESPONSE_GENERATED` | REPORTED |
| Edit/patch to files | Agent Event / Evidence | `PATCH_GENERATED` / `GENERATED_PATCH` | REPORTED |
| Session transcript file | Evidence | `AGENT_SESSION` | REPORTED |
| Commit created | Evidence | `GIT_COMMIT` | OBSERVED |

## Gaps

- Model may be `UNKNOWN` unless surfaced.
- Exact-line authorship is not proven by a transcript; keep generation `PARTIALLY_VERIFIED` until Git corroborates.

## Corroboration

- Transcript (`AGENT_SESSION`, REPORTED) + commit (`GIT_COMMIT`, OBSERVED) → `PARTIALLY_VERIFIED` for artifact/timing.

## Notes

This adapter matches the running example in [`../examples/`](../examples/), where Claude Code generates `src/auth/token.ts`.
