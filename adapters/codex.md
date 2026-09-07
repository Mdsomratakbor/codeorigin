# Adapter: Codex

**Status:** Optional. Does not modify the canonical model.

Maps signals from Codex-based coding agents onto CodeOrigin schemas.

## Agent Identity

```text
agent_id:     agent/codex
agent_name:   Codex
provider:     OpenAI (if known)
model:        model id if surfaced, else UNKNOWN
session:      session/conversation id if available
environment:  CLI / IDE / CI (as applicable)
```

## Signal Mapping

| Codex signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Session/conversation start | Agent Event | `SESSION_STARTED` | REPORTED |
| Human prompt | Agent Event | `PROMPT_SUBMITTED` | REPORTED |
| Generated code/patch | Agent Event / Evidence | `PATCH_GENERATED` / `GENERATED_PATCH` | REPORTED |
| Applied file changes | Agent Event | `FILE_MODIFIED` etc. | REPORTED |
| Commit created | Evidence | `GIT_COMMIT` | OBSERVED |

## Gaps

- Provider/model may be `UNKNOWN`.
- Session logs may not be retained; then generation is `REPORTED` from whatever record exists, or `UNKNOWN`.

## Corroboration

- Match `GENERATED_PATCH` (REPORTED) to the diff of a `GIT_COMMIT` (OBSERVED) to raise confidence.

## Notes

Do not infer that a change came from Codex based on code style. Identity requires a session/tool signal.
